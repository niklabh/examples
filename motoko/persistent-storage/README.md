# Persistent storage
The example dapp shows how to build a simple dapp in Motoko, which will have persistent storage. The dapp is a simple counter, which will increment a counter, retrieve the counter value and reset the counter value by calling backend functions. The functions are exposed through a Candid interface.

![Counter Frontend](README_images/candid_ui.png)

## Introduction
The purpose of this example dapp is to build a simple counter dapp, where the counter value will persist even after the dapp has changed and been re-deployed.

This example covers:

- Create new canister smart contract using Motoko
- Add backend functions for a counter (increment, get count and reset count)
- Deploy the canister smart contract locally
- Test backend with Candid UI and command line using `dfx` 

## Installation
This example project can be cloned, installed and deployed locally, for learning and testing purposes. The instructions are based on running the example on either macOS or Linux, but when using WSL2 on Windows, the instructions will be the same.

### Prerequisites
The example project requeres the following installed:

- git
- dfx

git can be installed from various package managers. `dfx` can be installed following the instructions [here](https://smartcontracts.org/docs/quickstart/local-quickstart.html#download-and-install).

### Install
Install the example dapp project:

```bash
$ git clone https://github.com/dfinity/examples
$ cd examples/motoko/persistent-storage
```

## Documentation
The two main parts of the example dapp are the backend and the Candid interface. This example project does not have a frontend.

### Motoko backend
The backend functions are located in the `src/persistent_storage/main.mo` Motoko file. The backend stores the counter value, and has functions to get, increment and reset the counter value. Furthermore the backend insures the counter value persists upgrades of the dapp.

#### Counter variable
The current counter value is stored as a number in the actor.

```javascript
actor {
    stable var counter : Nat = 0;
}
```

#### increment()
The `increment()` function increments the counter variable.

```javascript
public func increment() : async Nat {
    counter += 1;
    return counter;
};
```

The function is returning the incremented counter variable.

#### get()
The `get()` function returns the current counter value.

```javascript
public query func get() : async Nat {
    return counter;
};
```

#### reset()
The `reset()` function resets the counter value to 0 and returns the value.

```javascript
public func reset() : async Nat {
    counter := 0;
    return counter;
};
```

### Candid interface
The Candid interface is automatically created, and it has a convenient UI, which provides an easy, user-friendly way to test the backend. Learn how to access the Candid UI in the **Testing** section below. 

## Deployment
The local network is started by running this command:

```bash
$ dfx start --background
```

When the local network is up and running, run this command to deploy the canisters:

```bash
$ dfx deploy
```

## Testing
There are two ways of testing the functionality of this example dapp. One way is by making command line requests using DFX, and the other way is to use the Candid UI. Before the example dapp can be tested, it must be deployed (locally) as described in the above Deployment section.

### dfx
`dfx` has a subset of commands for canister operations, and one of them enables calling the public functions added to the `main.mo` file in the previous step. In the following examples the initial value is 0. `increment` will increment value and return 1, `get` will return the current value and `reset` will set the value to 0.

Command usage: `dfx canister call <project>  <function>`

```bash
$ dfx canister call persistent_storage increment
(1 : Nat)
```

```bash
$ dfx canister call persistent_storage get
(1 : Nat)
```

```bash
$ dfx canister call persistent_storage reset
(0 : Nat)
```

The persistence of the stored value can be tested by calling the `increment` function again and making sure the value is larger than zero. Then make a change in the Motoko code, any minor change like changing the function name `get` to `getCount`, and then re-deploy the project and call the `getCount` function to verify that the counter value did not change back to the initial value (0).

```bash
$ dfx canister call persistent_storage increment
(1 : Nat)
// Make code change
$ dfx deploy
$ dfx canister call persistent_storage getCount
(1 : Nat)
```


### Candid UI
The Candid UI provides an easy, user friendly interface for testing the backend. The UI is automatically generated, and the canister ID can be found by using the `dfx canister id <canister_name>` command:

```bash
$ dfx canister id __Candid_UI
r7inp-6aaaa-aaaaa-aaabq-cai
$ dfx canister id persistent_storage
rrkah-fqaaa-aaaaa-aaaaq-cai
```

Go to: **http://<candid_canister_id>.localhost:8000/?id=<backend_canister_id>**

```bash
$ echo "http://$(dfx canister id __Candid_UI).localhost:8000/?id=$(dfx canister id persistent_storage)"
http://r7inp-6aaaa-aaaaa-aaabq-cai.localhost:8000/?id=rrkah-fqaaa-aaaaa-aaaaq-cai
```

![Candid UI](README_images/candid_ui.png)

## License
This project is licensed under the Apache 2.0 license, see LICENSE.md for details. See CONTRIBUTE.md for details about how to contribute to this project.
