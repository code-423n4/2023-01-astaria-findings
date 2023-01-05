The deposit function on vault contract transfer from the msg.sender to the owner 

‘’’

deposit(uint256 amount, address receiver)

…

require(s.allowList[msg.sender] && receiver == owner());

‘’’

The function makes sure that the receiver is the owner,
However the receiver has no role, and the arg must be always the owner 
This all can be avoided as this consumes more unnecessary gas,

Fix: remove the receiver arg and remove the receiver is the owner condition if you want to deposit to the owner , or remove the whole role 

‘’’

function deposit(uint256 amount) // here
    public
    virtual
    returns (uint256)
  {
    VIData storage s = _loadVISlot();
    require(s.allowList[msg.sender]); // here 
    ERC20(asset()).safeTransferFrom(msg.sender, address(this), amount);
    return amount;
  }

‘’’ 