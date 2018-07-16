# Events History smart contracts library [![Build Status](https://travis-ci.org/ChronoBank/solidity-eventshistory-lib.svg?branch=master)](https://travis-ci.org/ChronoBank/solidity-eventshistory-lib) [![Coverage Status](https://coveralls.io/repos/github/ChronoBank/solidity-eventshistory-lib/badge.svg?branch=master)](https://coveralls.io/github/ChronoBank/solidity-eventshistory-lib?branch=master)

Part of [LaborX project](https://github.com/ChronoBank). Organizes a set of smart contracts that can manage events emitting. _Using this approach all events should have their own public function for emitting_!

- **EventsHistory** - defines a contract that could allow or forbid events with definite signature and source;
- **MultiEventsHistory** - has less restrictions than **EventsHistory**, allows to filter events according to an authorized source;
- **MultiEventsHistoryAdapter** - base contract, other contracts should inherit from it to receive an ability to store events history contract as a separate address and redirect all events to provided contract.

## Installation

Organized as npm package this smart contracts could be easily added to a project by

```bash
npm install -s solidity-eventshistory-lib
```

## Usage

Right before you decided to use them add this library to package dependencies and import any contract according to this pattern, for example:

```javascript
import "solidity-shared-lib/contracts/MultiEventsHistoryAdapter.sol";
```

or

```javascript
import "solidity-shared-lib/contracts/MultiEventsHistory.sol";
```

## Details

Contracts that wants to use events history contract should go with a couple of rules:

1. Define a contract that will hold events history address (it is recommended to inherit from _MultiEventsHistoryAdapter)

```javascript
contract Manager is MultiEventsHistoryAdapter {
	event LogManagerEvent(address indexed self, uint at);
	
	//...
}
```

2. Add a public function with event emitting action (__self()_ is a function from _MultiEventsHistoryAdapter_ contract):

```javascript
//...
function emitManagerEvent(uint _at) public {
	emit ManagerEvent(_self(), _at);
}
//...
```

3. In your business logic you should invoke event emitting with the next command:

```javascript
//...
function actionExample() external returns (bool) {
	//...
	Manager(getEventsHistory()).emitManagerEvent(now);
	return true;
}
//...
```

Since events history contract will be the source of events then to keep information who was the actual source you need to reserve one of the parameters for passing __self()_ together with event.

	For more information and use cases look at tests.