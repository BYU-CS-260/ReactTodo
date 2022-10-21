# React Todo

In this learning activity, you will learn how to create a Todo list with the ability to mark items as complete and to delete completed items.  Along the way, you should learn a lot about how to handle events and how to manipulate the DOM.

1. First, create your index.html file with a form where the user can enter new items.  Test it to make sure everything is working.  The key to sucessfully implementing a complex application is to test every piece of the application.  We are keeping everything in one component here, but you could experiment in creating a different component for each piece of the application. Then you could test each component seperately.
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <script src="https://unpkg.com/react@18/umd/react.development.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js" crossorigin></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  </head>
  <body>
    <div id="root"></div>
    <script type="text/babel">  
    class Todo extends React.Component {
      constructor(props) {
        super(props);
         this.state = {
            formtask: '', 
        };
    
        this.handleChange = this.handleChange.bind(this);
        this.handleSubmit = this.handleSubmit.bind(this);
      }
    
      handleChange(event) {
        this.setState({formtask: event.target.value});
      }
    
      handleSubmit(event) {
        alert('A task was submitted: ' + this.state.formtask);
        event.preventDefault();
      }
    
      render() {
        return (
            <div>
              <h1> Todo List </h1>
              <form onSubmit={this.handleSubmit} onKeyUp={this.handleChange}>
                <label>
                  Name:
                  <input type="text" value={this.state.formtask} onChange={this.handleChange} />
                </label>
                <input type="submit" value="Submit" />
              </form>
            </div>
        );
      }
    }
    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(<Todo />);
    </script>
  </body>
</html>
```

2. Now we need to add an array of tasks to your state. These tasks will be displayed below the form.  We will put in some dummy tasks so we can see if things are working.  You can remove them once you can add tasks.  Notice that our task array has a completed field so that we can check them off.

```jsx
        this.state = {
            formtask: '', 
            tasks:[{"task":"Wash Clothes","completed":false},{"task":"Sweep Floor","completed":true}]
        };
```
3. And add code at the top of render (before the return) to create the list.  
```jsx
        const listItems = this.state.tasks.map((thistask, index) => 
          <li key={thistask.task}>{thistask.task}</li>
        );
```
4. Then add code to display the list below the form.  Test to make sure that the two dummy tasks are displayed below the form.
```jsx
            <ul>{listItems}</ul>
```
5. Now we need to insert formatting so that items that have been completed will have the strikeout style.  Create a file "index.css" with the following content:
```css
.todo {
  cursor: pointer;
}
.strike {
  text-decoration: line-through;
}
```
Add a link to the style sheet in the ```<head>``` of index.html.
```html
<link rel="stylesheet" href="index.css">
```
And change the way that the "listItems" are created so that a different style is used if the task is completed.  The "Sweep Floor" task should have a line-through it.
```jsx
        const listItems = this.state.tasks.map((thistask, index) => 
          <li className={thistask.completed ? "strike" : "todo"} 
            key={thistask.task}>{thistask.task}</li>
        );
```
6. Now let's create a way of adding elements to the Todo list.  "setState" tells React to change that part of the DOM, so we will set the state element "tasks" to have the current content of tasks "...this.state.tasks" concatenated to a new object with the contents of "formtask" and "completed" set to false.  We will then clear the form by setting the formtask to "".  When we change the contents of this javascript variable, React automatically changes what is seen in the DOM.  Test to make sure that you can add tasks to your Todo list.
```jsx
      handleSubmit(event) {
        // alert('A task was submitted: ' + this.state.formtask);
        this.setState({tasks:[...this.state.tasks, 
            {"task":this.state.formtask,"completed":false}]
        });
        this.setState({formtask:""});
        event.preventDefault();
      }
```
7. Now that we can add elements to the Todo list, we need to allow the user to mark them as complete.  To do this, we will add a callback when the user clicks on any of the elements.  Modify the creation of the list to include this callback.
```jsx
        const listItems = this.state.tasks.map((thistask, index) => 
          <li className={thistask.completed ? "strike" : "todo"} 
            onClick={event => this.handleToggle(event, index)}
            key={thistask.task}>{thistask.task}</li>
        );
```
We need to create a callback "handleToggle" that will be passed the event and the index of the event so that the callback can change the item in the array.  Add "handleToggle" to the constructor
```jsx
        this.handleToggle = this.handleToggle.bind(this);
```
And add the body of the function after "handleSubmit".  
Notice that we first look up "target" using the index that was passed to us from the list.  
We change the completed status of "target", then we toggle the css to change the strikethrough.  Test to make sure that you can toggle the strikethrough on your Todo list.  Open up your inspect window to make sure that the completed field is being set correctly after the state is toggled.
```jsx
      handleToggle(event, index)  {
        const element = event.target;
        var target = this.state.tasks[index];
        if(target.completed) {
            target.completed = false;
        } else {
            target.completed = true;
        }
        console.log(this.state.tasks);
        element.classList.toggle("strike");
      }
```
8. Now we need to create a way to delete completed items.  First create a "Clear" button at the bottom of our ```<ul>```.
```jsx
            <button onClick={this.handleFilter}>Clear Completed</button>
```
This button will call the "handleFilter" callback, so add the bind call in the constructor.
```jsx
        this.handleFilter = this.handleFilter.bind(this);
```
And add the callback under "handleToggle".  We will filter out anything that is completed and set the "tasks" state to this filtered list.
```jsx
      handleFilter() {
        let filtered = this.state.tasks.filter(task => {
          return !task.completed;
        });
        this.setState({tasks:filtered});
        event.preventDefault();
      }    
```
