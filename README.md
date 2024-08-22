### Step to build package
```console
python -m build
python -m twine upload --repository pypi
```

### Install package
```console
    pip install biwoco==0.0.2
```

### Using Services
```console
    import biwoco 
    biwoco.exchange_rate(base='USD')
```

### Result
```console
 from pymongo import MongoClient
from pymongo.encryption import AutoEncryptionOpts, ClientEncryption
from bson.binary import Binary
import os

# Set up key configuration
key_vault_namespace = 'encryption.__keyVault'
kms_providers = {
    'local': {
        'key': os.urandom(32),  # Create a temporary key
    }
}

# Connect to MongoDB
client = MongoClient('mongodb://localhost:27017')

# Create client encryption
client_encryption = ClientEncryption(
    kms_providers,
    key_vault_namespace,
    client,
)

# Create encryption key
key_id = client_encryption.create_data_key('local')

# Auto encryption options
auto_encryption_opts = AutoEncryptionOpts(
    kms_providers,
    key_vault_namespace,
    schema_map={
        "mydatabase.secure_data": {
            "$jsonSchema": {
                "bsonType": "object",
                "properties": {
                    "ssn": {
                        "encrypt": {
                            "bsonType": "string",
                            "keyId": key_id,
                            "algorithm": "AEAD_AES_256_CBC_HMAC_SHA_512-Deterministic"
                        }
                    }
                }
            }
        }
    }
)

# Create MongoDB client with auto encryption
encrypted_client = MongoClient('mongodb://localhost:27017', auto_encryption_opts=auto_encryption_opts)

# Select database and collection
db = encrypted_client['mydatabase']
collection = db['secure_data']

# Encrypt and store document (now just store without manually calling encryption function)
document = {
    'username': 'user1',
    'password': 'super_secret_password',
    'ssn': '123-45-6789'  # Store without manual encryption
}
collection.insert_one(document)

# Retrieve and automatically decrypt document
retrieved_document = collection.find_one({'username': 'user1'})
print(retrieved_document)  # SSN automatically decrypted
```
