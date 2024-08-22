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

