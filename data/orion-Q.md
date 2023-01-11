The contracts on the require() function uses very long revert messages, as example :

```
CashKYCSender: must be KYC'd to initiate transfer
CashKYCSender: `from` address must be KYC'd to send tokens
only the admin may call _resignImplementation
only the admin may call _becomeImplementation
```

...etc 

However there's something about solidity which is that the longer is the revert message the more gas is being consumed
see : https://www.youtube.com/watch?v=59MRDldSItU

it's recommended to use less chars as revert messages, example "

```
!admin
from!=kyced
```

============



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