# React Notes

React is a JavaScript library for building user interfaces.
It is all about components.

## Create React Project:
```shell
npx create-react-app app-name
```

## Project structure and files:
1. src/index.js: first file to be executed
2. public/index.html: only HTML file present in application
3. src/App.js: React component containing JSX code -> JavaScript XML (JSX).

We can see compiled JS files generated from JSX files using Chrome Developer tools -> Sources -> localhost:3000/static/js -> main.chunk.js.

## Components:
* building blocks in user interface. Preferably must be reusable.
* src/components: create one file per component
* We can organize component files into subfolders like Expense, UI (general UI)
* App.js to be kept outside components folder as it will be the root component
* Name components as ExpenseItem.js
* We must start custom component names with uppercase characters as React detects them as custom components and lowercase ones as HTML elements
* Example:
	```jsx
	function ExpenseItem() {
		return <h2>ExpenseItem</h2>;
	}
	export default ExpenseItem;
	```
	Import in App.js as:
	```jsx
	import ExpenseItem from './components/ExpenseItem';

	function App() {
		return (
			<div>
			<ExpenseItem></ExpenseItem>
			</div>
		);
	}
	```
* We must always return one parent element per React component, invalid code:
	```jsx
	return <div>Date</div><div>Amount</div>; // invalid
	```
* Solution is to wrap the component into a parent div
	```jsx
	return <div><div>Date</div><div>Amount</div></div>; // valid
	```
* **Styling**: create file ExpenseItem.css in components folder and import it in ExpenseItem.js as:
	```jsx
	import './ExpenseItem.css';
	```
* **class** attribute in HTML is replaced by **className** attribute in JSX
* Display dynamic data using:
	```jsx
	{expenseData}
	```
  We can run any JS code inside **{}**
* We can pass data to using props:
	```jsx
	// in parent component
	<ExpenseItem title={expenses[0].title}></ExpenseItem>
	```
	```jsx
	// in child component
	function ExpenseItem(props) { // props can be any name
		return props.title;
	}
	```
* Nesting inside custom components:
	```jsx
	// in ExpenseItem component
	<Card className="expense-item">
		<div className="expense-item__description">
		</div>
	</Card>
		
	// in Card component
	function Card(props) {
		// className and children are passed by default to every component
		// className is the className attribute provided when using this component
		// We cannot use custom components as wrappers to other components
		// Without props.children nested components will not be displayed
		// children is the nested JSX provided inside Card component
		const classes = 'card ' + props.className;
		return <div className={classes}>{props.children}</div>;
	}

	export default Card;
	```
* **Working of JSX**: Previously, React components used to return React.createElement('div', {attribute: value}, React.createElement('div')). This syntax is no longer used in newer versions of React. Behind the scenes, JSX gets converted to this syntax. This is the reason, we can have only single parent element in a component. Hence, we can have an optional import as below:
	```jsx
	import React from 'react';
	```
* We can also use arrow functions to define functional components as below:
	```jsx
	const Expense = (props) => {
		return <div></div>;
	}

	export default Expense;
	```
* **Handling events**: 
	```jsx
	<button onClick={clickHandler}>btn1</button>

	const clickHandler = (e) => { // inside component function
		console.log(e);
	};
	```
	Name event handler functions like 'clickHandler'.

## State:
* If we reassign any custom variable in React component, the DOM is not updated to show updated value. We must use state if we want DOM to re-render.
* Inside component function, we must call **useState**();. useState is a React Hook. All React Hook have use in their name. Every React Hook must only be called within component function. Also, they must not be called within nested functions.
	```jsx
	const [title, setState] = useState(props.title);
	// useState takes initial value as argument
	// returns array of value and setter function
	const clickHandler = () => {
		setState('New Title'); // runs asynchronously (scheduled)
		console.log(title); // old title as setState schedules change to title but value isn't immediately available
	};
	```
* State is managed on a per component basis
* **Single state approach**: If we have multiple values to be tracked as state, we can create separate state objects for them or can handle them using single state object. It is recommend to split state into multiple state variables based on which values tend to change together.
	```jsx
	// Single state approach
	const [userInput, setUserInput] = useState({
		enteredTitle: '',
		enteredAmount: '',
		enteredDate: ''
	});
	// Do not use this for setUserInput when using previous state as it may be incorrect
	const titleChangedHandler = (event) => {
		setUserInput({ ...userInput, enteredTitle: event.target.value });
	};
	// Use below function with prevState param to update userInput
	const titleChangedHandler = (event) => {
		setUserInput((prevState) => {
			return { ...prevState, enteredTitle: event.target.value };
		});
	};
	```

## Forms:
* We can call **onChange** event on form elements and pass reference to some function
	```jsx
	// event.target.value always returns string hence initialize with empty string
	const [enteredDate, setEnteredDate] = useState('');

	const dateChangedHandler = (event) => { // event passed automatically
		setEnteredDate(event.target.value); // current value in field
		// will always be string even for date and number types
	};

	<input type="date" onChange={dateChangedHandler}></input>
	```
* **Submitting forms**:
	```jsx
	<form onSubmit={submitHandler}>

	const submitHandler = (event) => {
		event.preventDefault(); // prevent reloading page when form submitted
		const expenseData = {
			title: enteredTitle,
			amount: enteredAmount,
			date: new Date(enteredDate)
		};
	};
	```
* **Two way binding**:
	```jsx
	<input
		type="text"
		onChange={titleChangedHandler}
		value={enteredTitle} // performs two way binding
	></input>

	const submitHandler = (event) => {
		event.preventDefault(); // prevent reloading page when form submitted
		const expenseData = {
			title: enteredTitle,
			amount: enteredAmount,
			date: new Date(enteredDate)
		};
		console.log(expenseData);
		setEnteredTitle(''); // resets the form after submit
		setEnteredAmount('');
		setEnteredDate('');
	};
	```