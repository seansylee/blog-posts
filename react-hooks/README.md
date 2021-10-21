## **Introduction to Custom hooks in React** 
Consider the typical behavior of a React component: we render the component based on its _**States**_ and subscribe to the changes by its _**Effects**_. With the introduction of _Hooks_ such as `useState()` and `useEffect()` in React (v16.8), we are able to create function components and simplify the behavior in our components with easy to read stateful logic.

While these hooks allow us to conveniently use stateful logic in our function components, without the ability to extract certain reusable logic we will find our component to grow larger and larger. So this begs the question: **_Can_ we extract stateful logic from our components?** 

As you can guess, _yes you can_! Bringing us to the topic of this post: **Custom Hooks,** lets get right into it. 

___

### **🤔 Something doesn't seem right!**

Lets take a look at this simple text input component:
```js
const Input = () => {

  const [value, setValue] = useState(''); 

  const handleOnChange = (e) => {
    setValue(e.target.value);
  }
   
  return(
    <div>
      <input value={value} onChange={(e)=>handleOnChange(e)} />
    <div/>
  );
}
```
We set the value rendered in the input as a state `value`, and on change we invoke the `handleOnChange` function which in term updates the state to the input target value via `setValue`.

(If you are unfamiliar with the `target` property in the Even and the Web API you can refer to this [here](https://developer.mozilla.org/en-US/docs/Web/API/Event/target))

Now suppose we want to be a little bit more specific with our component and decide that we will use this component to capture a user's full name.

```js
const NameInputField = () => {

  const [firstName, setFirstName] = useState('Peter'); 
  const [lastName, setLastName] = useState('Parker');

  const handleOnFirstNameChange = (e) => {
    setFirstName(e.target.value);
  }
  const handleOnLastNameChange = (e) => {
    setLastName(e.target.value);
  }
   
  return(
    <div>
      <input value={firstName} onChange={(e)=>handleOnFirstNameChange(e)} />
      <input value={lastName} onChange={(e)=>handleOnLastNameChange(e)} />
    <div/>
  );
}
```
🤔.. Something doesn't seem quite right doesn't it? 

While a good approach/workaround would be to reuse the `<Input />` component for both first and last names, for the sake of this post we will find a custom hook oriented solution! 


---
### **💡 They are all just _Functions_!**

Before discussing our solution lets take a quick step back to go over an important observation.

While working with React components and its lifecycles, it is easy to over complicate the simple things that it can be daunting to think about how our component and its hooks are working together. But in fact _**components and hooks are just functions!**_ 

If you are able to take anything from reading this, it is that we do not need to overcomplicate the fact that we are simply just dealing with functions.

Well working with functions are quite simple: You give it something (sometimes nothing) and get something back in return. 
```
// Basic function.

Something ---> Function ===> Something Else! 
```

Well now that we've simplified things to just functions.. How do you share logic between functions? 

...

Yeap, you guessed it: We just extract it!

---
### **Custom hook to the rescue! ✌️** 

Lets get back to our code! 


We see that the `handleOnFirstNameChange()` and `handleOnLastNameChange()` functions perform the same task: Receive a name value and update the name state. 

So lets extract it:

In writing hooks the best practice is to use the `"use"` keyword to indicate that what we are writing is in fact a hook that inhibits a stateful logic.

lets call it `useValue()`

```js
const useValue = (initialValue) => {
  const [value, setValue] = useState(initialValue);

  const onChange = (e) => {
    setValue(e.target.value);
  } 

  return {
    value,
    onChange
  }
}
```
We practically took the same logic, and extracted it out to form a custom hook. _Just like you would any other function!_

Now to "use" it in our component we again treat it like its just a function (because it is) 

```js
const useValue = (initialValue) => {
  const [value, setValue] = useState(initialValue);

  const onChange = (e) => {
    setValue(e.target.value);
  } 

  return {
    value,
    onChange
  }
}


const NameInputField = () => {
  const firstName = useValue('Peter');
  const lastName = useValue('Parker');

  return(
    <div>
      <input value={firstName.value} onChange={firstName.onChange} />
      <input value={lastName.value} onChange={lastName.onChange} />
    <div/>
  );
}
```
Simple right?!

If you are familiar with the [ES6 spread operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) we can apply more magic to our custom hook.

```js
const NameInputField = () => {
  const firstName = useValue('Peter');
  const lastName = useValue('Parker');

  return(
    <div>
      <input {...firstName}/>
      <input {...lastName}/>
    <div/>
  );
}
```
Now with that we have successfully created our custom hook! That wasn't so hard was it?

Hope you were able to find this helpful!

Thank you for reading ☺️.
