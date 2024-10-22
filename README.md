# -Redux-Checkpoint

Créer une application ToDo avec Redux implique plusieurs étapes. Voici un exemple simplifié d'une telle application avec les composants AddTask, ListTask, et Task, ainsi que la gestion de l'état avec Redux.

###1. Installation des dépendancesTout d'abord, exécutez les commandes suivantes pour installer react, redux, react-redux et @reduxjs/toolkit (qui est recommandé pour créer un store Redux avec moins de configuration) :

bashnpx create-react-app todo-appcd todo-appnpm install redux react-redux @reduxjs/toolkit

###2. Configuration de ReduxCréez un fichier store.js pour configurer le store :

javascript//

const tasksSlice = createSlice({  
 name: 'tasks',  
 initialState: [],  
 reducers: {  
 addTask: (state, action) => {  
 state.push({ id: Date.now(), description: action.payload, isDone: false });  
 },  
 toggleTask: (state, action) => {  
 const task = state.find(task => task.id === action.payload);  
 if (task) {  
 task.isDone = !task.isDone;  
 }  
 },  
 editTask: (state, action) => {  
 const { id, description } = action.payload;  
 const task = state.find(task => task.id === id);  
 if (task) {  
 task.description = description;  
 }  
 },  
 filterTasks: (state, action) => {  
 return state.filter(task => action.payload === 'all' || (action.payload === 'done' ? task.isDone : !task.isDone));  
 },  
 },  
});  

export const { addTask, toggleTask, editTask, filterTasks } = tasksSlice.actions;  

const store = configureStore({  
 reducer: { tasks: tasksSlice.reducer },  
});  

export default store;  
###3. Configuration du ProviderDans votre fichier index.js, enveloppez votre application avec le Provider de react-redux :

javascript//
import ReactDOM from 'react-dom';  
import { Provider } from 'react-redux';  
import store from './store';  
import App from './App';  

ReactDOM.render(  
 <Provider store={store}>  
 <App />  
 </Provider>,  
 document.getElementById('root'),  
);  
###4. Création des composants#### AddTask.js```javascript// src/components/AddTask.jsimport React, { useState } from 'react';
import { useDispatch } from 'react-redux';
import { addTask } from '../store';

const AddTask = () => {
const [description, setDescription] = useState('');
const dispatch = useDispatch();

const handleSubmit = (e) => {
e.preventDefault();
if (description.trim()) {
dispatch(addTask(description));
setDescription('');
}
};

return (

<form onSubmit={handleSubmit}> <input type="text" value={description} onChange={(e) => setDescription(e.target.value)} placeholder="Add a new task" /> <button type="submit">Add Task</button> </form> ); };
export default AddTask;

ini

#### Task.js```javascript// src/components/Task.jsimport React from 'react';  
import { useDispatch } from 'react-redux';  
import { toggleTask, editTask } from '../store';  

const Task = ({ task }) => {  
 const dispatch = useDispatch();  

 const handleToggle = () => {  
 dispatch(toggleTask(task.id));  
 };  

 const handleEdit = () => {  
 const newDescription = prompt("Edit task description:", task.description);  
 if (newDescription) {  
 dispatch(editTask({ id: task.id, description: newDescription }));  
 }  
 };  

 return (  
 <div>  
 <input type="checkbox" checked={task.isDone} onChange={handleToggle} />  
 <span>{task.description}</span>  
 <button onClick={handleEdit}>Edit</button>  
 </div>  
 );  
};  

export default Task;  
ListTask.js```javascript// src/components/ListTask.jsimport React from 'react';
import { useSelector } from 'react-redux';
import Task from './Task';

const ListTask = ({ filter }) => {
const tasks = useSelector((state) => state.tasks);
const filteredTasks = filter ? tasks.filter(task => filter === 'done' ? task.isDone : !task.isDone)
: tasks;

return (

<div> {filteredTasks.map(task => ( <Task key={task.id} task={task} /> ))} </div> ); };
export default ListTask;

css

###5. Création du composant principal App.js```javascript// src/App.jsimport React, { useState } from 'react';  
import AddTask from './components/AddTask';  
import ListTask from './components/ListTask';  

const App = () => {  
 const [filter, setFilter] = useState('all');  

 return (  
 <div>  
 <h1>ToDo App</h1>  
 <AddTask />  
 <div>  
 <button onClick={() => setFilter('all')}>All</button>  
 <button onClick={() => setFilter('done')}>Done</button>  
 <button onClick={() => setFilter('notDone')}>Not Done</button>  
 </div>  
 <ListTask filter={filter} />  
 </div>  
 );  
};  

export default App;  
ConclusionCe code fournit une structure de base pour une application ToDo en utilisant Redux pour gérer l'état global. Vous pouvez exécuter l'application et tester les fonctionnalités d'ajout, de filtrage et de modification des tâches. N'hésitez pas à le personnaliser davantage en ajoutant des styles ou d'autres fonctionnalités selon vos besoins.
