https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L348-L349
changing from __s.guardian = address(0);
    s.newGuardian = address(0);__
to
__delete  s.guardian;
    deletes.newGuardian;__ can save some gas