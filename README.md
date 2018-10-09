# MobX = {
This is how we do MobX here at Evermind. It's a list of best practices and patterns that we've found useful over time in building the powerful beast that is [Orulo](https://orulo.com).

---

## Contents
1. [Principles](#principles)
2. [Syntax, Basic Conventions](#syntax-basic-conventions)
3. [Patterns, Architechture](#patterns-architecture)

---

## 1. Principles

- [1.1] - **Have as few sources of truth as possible -** You should use `@computed` as often as possible to keep the number of `@observable` properties to a minimum in your store. Think of your store like a waterfall, with a source of data at the top which flows down to computed properties.
- [1.2] - **Functional porgramming is your friend -** Although MobX blends paradigms and isn't as strictly functional as Redux is, it is useful to keep things pure and functional in many cases.

[⬆ back to top](#contents)

---

## 2. Syntax, Basic Conventions

- [2.1] **Prefer decorators** - Decorators are the preferred method for using MobX (where possible). Although decorators aren't official supported yet, we find them to be vey convenient and far prettier than other alternatives.
  ```javascript

	// good
	@observable numbers = [1, 2, 3];
	@computed get total() {
		return this.numbers.reduce((acc, cur) => acc + cur);
	}

	// bad
	numbers = observable([1, 2, 3]);
	// or
	decorate(OrderLine, {
		numbers: observable,
		total: computed
	})
	```

- [2.2] **Use Strict Mode** - You shouldn't be modifying `@observables` outside of actions. The following helps enforce this good practice.
	```javascript
	mobx.configure({ enforceActions: true })
	```

- [2.3] **Always use actions to modify observables** (related to 2.2) - This helps you MobX batch actions together and is much more explicit code.

- [2.3] **Use `@Inject` and `<Provider>`** - None of that 6 layers of passing props down thank you very much.
  ```javascript

	// in the render mehtod of a high level component/view
	render() {
		const stores = { storeOne, storeTwo, storeThree };
		return (
			<Provider {...stores}
				YOUR APP
			</Provider>
		);
	}
	
	// in your low-level component
	@inject('storeOne')
	@computed
	class Component extends React.Component {
		// ...
	}
	```

- [2.4] **`@Computed` functions should be pure`** - These are getters which should have no side-effects.

- [2.5] **Loading state goes on the store, not the view** - As we typically have a store for any views where data is loaded, this is easy to handle. You should have an `isLoadingX` property for each thing on your store, not one general property.

- [2.6] **Use these naming conventions for actions ont eh client and for communicating with the API**
|   | Server | Client  |
|---|--------|---------|
| C | Create | Add     |
| R | Fetch  | -       |
| U | Update | various |
| D | Delete | Remove  |


**[⬆ back to top](#contents)**

---

## 3. Patterns,  Architecture

- [3.1] - **File structure**
	- We have several different kinds of components in our React project, roughly following an MVC architecture.
		- Views - high-level components, typically occupying a whole page.
    - Components - self-explanatory
		- Models - Classes which are used to represent our API data.
    - Actions - where we pull in our API data.
    - Stores - Where we manage and store our MobX state.
	- This is roughly what project might look like:
		```
		Javascript:.
		├───Views
		│   ├───DataView.js
		├───Components
		│   ├───DataViewTable.js
		│   └───Toolbar.js
		├───Models
		│   └───ReportTemplateModel.js
		├───Stores
		│   ├───DataViewStore.js
		│   └───AccountStore.js
		├───Actions
		│   ├───DataViewActions.js
		│   └───AccountActions.js
		...
		```
- [3.1] - Models
  - `@observable` is not needed on immutable properties, e.g. id.
  - You should destructure all the properties on your models.
  - Updating and modifying actions should go on the model, not the store.
  - Optional properties should be marked accordingly with Flow but should reflect the full API model.

- [3.2] - Stores
  - **When do you need a store?** - Typically, we created a store for a new, large view or for a set of data that will occur across the app. There are no hard and fast rules about this so use your own judgement.
  - You should only have one instance of each store
  - You should make the store both a defaut and non-default export. Default should be an instance of the store, non-default should be of the class itself, used for typing with FlowJs.
		```javascript
		export class DataViewStore = {
			// ...
		}

		const store = new DataViewStore();
		export default store;
		```

- [3.3] - Nested Data
  - You should keep data nested. (EXPLAINATION TO COME)


## TODOs
These are patterns and elements that need to be discussed, thought-through and/or added to the above sections.
- [] **Modifying an element at a given index in an observable array**
- [] **Nested data** WHY do we not flatten data? What are the drawbacks of this?
- [] **Actions** How do we write asynchronous code?
- [] **Stores**
	- [] Reset
	- [] Order of things on the store
- [] Saving
  -  we used to use isPersisted but this might change with mobx-rest.

**[⬆ back to top](#contents)**

---

# };

