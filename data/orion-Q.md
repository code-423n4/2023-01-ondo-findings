Function of CCashDelegate contract can all be avoided, the function of CCashDelegate are made just to ensure that. :

```
 require(
      msg.sender == admin,
      "only the admin may call _becomeImplementation"
    );
```

as both of the function have no other purpose but that, while this is just an extra code and all can be replaced, 
_becomeImplementation and _resignImplementation can be made as modifiers to ensure `msg.sender == admin` or remove them as there is no logical action they are making

(Manual review) 