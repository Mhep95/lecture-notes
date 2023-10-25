

# Adding Interactivity (contd.)

## Render and Commit -- More on Reactive Programming with React
Source: Adding Interactivity > [Render and Commit](https://react.dev/learn/render-and-commit)

Notes:
- the restaurant metaphor is not very useful

**What to understand**
- What triggers a component re-render? Change of state in that component
- Besides the re-rendered component, all it's children will also be re-rendered
- Multiple state changes might be batched in the same re-render
- Rendering is logical - react has a virtual DOM that it updates - then updates the real DOM only in those parts where this is necessary

## State Does Not Change Inside an Event Handler
Source: Adding Interactivity > [State as a Snapshot](https://react.dev/learn/state-as-a-snapshot)

Notes:
- the snapshot metaphor I don't find very useful
- the first image that shows react updating the DOM tree is good
- the state is not changed immediately after you call the setter, but later when React does all the state changes. Try to understand the [examples](https://react.dev/learn/state-as-a-snapshot#state-over-time)!

## Queueing A Series of State Updates
Source: Adding Interactivity > [Queueing A Series of State Updates](https://react.dev/learn/queueing-a-series-of-state-updates)

What to understand
- Updater Functions (cool concept, not needed often)
- Setting state does not change the variable in the existing render, but it will be changed in the  new render

## Updating Objects in State
Source: Adding Interactivity > [Updating Objects in State](https://react.dev/learn/updating-objects-in-state)

**What to understand**
- modifying an object is named **mutating an object**
- you don't mutate state objects in React because react does not *see* that
- modifying objects is *easiest* with the *spread syntax*: 
	![](./images/spread-syntax.png)
- updating nested objects is ... a little bit ugly
	![](./images/nested-object-definition.png)![](./images/updating-nested-objects.png)


## Updating Arrays in State
Source: Adding Interactivity > [Updating Arrays in State](https://react.dev/learn/updating-arrays-in-state)

**What to understand**
- you can use spread syntax to create a copy of an array, e.g. `[...artists]`
- once you have a copy, you can simply add elements at the end, e.g. 
![](images/adding-to-array.png)

- removing an element from an array: `filter` or `slice` (never use `splice` -- that one mutates the object)

![](images/filter-array.png)
- replacing an element in the array: `map` (one of the most important functions in functional programming -- Google invented a whole new big data processing around it -- see MapReduce)
![](images/map-reduce.png) 

- Inserting an element 
![](images/inserting-in-array.png)

- sorting, reversing - `reverse`, `sort` -- they mutate the array. But it's ok if you copy the array first, and then mutate it the way you like


# Managing State

- **Reacting to Input with State**
	- seems to me to me to be needlessly theoretical; basically
		- have a state variable
		- represent it with useState
		- render different states in terms of that
		- do not use redundant state variables (the whole discussion in Remove non-essential variables)
	- Promise example is nice in Step 5 - question is: did we talk promises yet? 
		- Once we understand the promise, we can also think about how to implement a "loading..." state
	- Challenges
		- 2. Profile Editor is a good one; everybody should be able to do this


# Escape Hatches

## Synchronizing with Effects
Source: Escape Hatches > [https://react.dev/learn/synchronizing-with-effects#step-1-declare-an-effect](https://react.dev/learn/synchronizing-with-effects#step-1-declare-an-effect)

- some components need to be synchronized with external systems (send a message to a server, save something in localStorage, etc.)

Effects let you run some code after rendering so that you can synchronize your component with some system outside of React

Two types of logic inside React components:
1. Rendering code => a transformation from props & state into JSX 
2. Event handlers => functions that do things -- change state, submit HTTP POST request, navigate to another screen, interact directly with the DOM (e.g. change page title)

When is this not sufficient?
Example
- when a ChatRoom component must connect to the server to download the corresponding info
- this is considered a side-effect (with respect to the main responsibility of the component which is to render something)

In general
- effects in React are directly caused by the rendering
- thus, they are called at the end of a commit, **after the screen updates**
- why is this a good time to sync components with external systems? 

How to define an effect?


#### 1. Declare it: `import { useEffect } from 'react';`

	- `useEffect` is a React Hook that lets you [synchronize a component with an external system.](https://react.dev/learn/synchronizing-with-effects) 
	- You always put hooks at the top of your component

```
function MyComponent() {  

	useEffect(() => {  
		console.log("every render!")
	});  
	return <div />;  
}
```

Every time the component renders, React updates the screen and then runs the code inside useEffect. 

Interesting example: [controlling a video player component](https://react.dev/learn/synchronizing-with-effects#step-1-declare-an-effect) with an effect
- you need to interact with the DOM
- get a reference to the relevant DOM element
- call the `start` and `stop` methods 

#### 2. Specify the Effect dependencies 

Observe in the example: 
- adding the empty dependencies array `[]` as a second argument to `useEffect` results in an error

![](images/missing-dependency.png)
- adding `isPlaying` as second argument fixes the problem

![](images/having-added-the-dependency.png)

### 3. Add cleanup if needed 

- If your effect allocates a resource that can be deallocated, do that by returning a *cleanup function*
- Pay attention at the syntax it's **an arrow function that does the connection, and then returns another function that does the disconnect** 
- Important: **If you call an effect with no dependencies, it is only run once, on mount!**

![](images/effect-with-cleanup-function.png)

- During development, React runs the useEffect twice on mount



# To think about
- why is reading from a server considered a side-effect in react? 