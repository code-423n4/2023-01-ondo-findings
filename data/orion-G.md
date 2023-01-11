_becomeImplementation and _resignImplementation function from. the CCashDelegate contract consumes more unnecessary gas by the following code :

```
if (false) {
      implementation = address(0);
    }
```

this will always be passed and will just consumes more gas, it's recommended to delete them