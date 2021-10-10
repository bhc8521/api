API

规范：
所有与链上交互的动作都需要near-api-js

与token或者near相关的数量都应乘以decimal
比如1个near的表示方法为 1000000000000000000000000, 他的decimal为24，24个0，用字符串表示
其他token的decimal可能不是24
gas费一般decimal为16，最大值为300000000000000

大于等于u64的数字都需要表示为字符串.

view方法不需要gas费也不能deposit near
call方法有的需要deposit, 但都需要gas费

各种前端调用例子可以参考https://github.com/near-examples/
或者参考已有的测试用前端代码： https://github.com/27s-io


一个最基础的例子可参考以下：
```
async function connect(nearConfig) {
  // Connects to NEAR and provides `near`, `walletAccount` and `contract` objects in `window` scope
  // Initializing connection to the NEAR node.
  window.near = await nearAPI.connect({
    deps: {
      keyStore: new nearAPI.keyStores.BrowserLocalStorageKeyStore()
    },
    ...nearConfig
  });

  // Needed to access wallet login
  window.walletConnection = new nearAPI.WalletConnection(window.near);

  // Initializing our contract APIs by contract name and configuration.
  window.contract = await new nearAPI.Contract(window.walletConnection.account(), nearConfig.contractName, {
    // View methods are read-only – they don't modify the state, but usually return some value
    viewMethods: ['get_num'],
    // Change methods can modify the state, but you don't receive the returned value when called
    changeMethods: ['increment', 'decrement', 'reset'],
    // Sender is the account ID to initialize transactions.
    // getAccountId() will return empty string if user is still unauthorized
    sender: window.walletConnection.getAccountId()
  });
}
```

此时我如果调用get_num则为： 
```
let res = await window.contract.get_num()
```

调用increment 
```
let res = await window.contract.increament({..args}, 300000000000000, "1000000000000000000000000")
```

