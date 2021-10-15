#### src/index.js
```js
import React from 'react';
import ReactDOM from 'react-dom';

import App from './App';

ReactDOM.render(<App>, document.getElementById('root'));
```
#### App.js
```js
import React from 'react';

const App= () => {
    return ()
        <div>
            <h1>Hello, World</h1>
        </div>
    )
}
export default App;
```
> install all the necessary the dependencies throughout the process of building our application
> npm install @material-ui/core @material-ui/icons @material-ui/lab @react-google-maps/api axios google-map-react
--------------------------------------------------------------------------------------------

#### Header.jsx
```jsx
import React from 'react';

const Header = () => {
    return (
        <h1>Header</h1>
    );
}
export default Header;
```
> the same way to create Map, List, PlaceDetails
> import them inside of our App.js
```js
import React from 'react';
// lets start importing some of the things from material ui for creating layout
import {CssBaseline, Grid} from '@material-ui/core';
// CssBaseline is a component from material ui that simply normalizes the styles 
// so it's just going to fix some paddings ,margins and background colors  

import Header from './components/Header/Header'
import List from './components/List/List'
import Map from './components/Map/Map'
import Header from './components/Header/Header'

const App = () => {
    return (
        <>
            // normailizer style
            <CssBaseline />
            <Header />
            // this grid is going to be of a type container
            // the container spacing is going to be equal to 3
            // and we can also give it an inline style that's going to be equal to width 
            // is equal to a String of 100%, since we're doing inline style 
            // it has to be an object so have double curly braces style ={{width:'100%'}} 
            <Grid container spacing={3} style={{ width:'100%' }}>
                // this grid is going to be of type item
                // and it's going to have two propertys
                // xs={12} this means this grid is going to take full width on mobile devices
                // md={4} then on medium devices and larger it's going to take full spaces
                // remerber that list was only showing on the left side and the map was taking 
                // much more space this is what makes it happen only take 4 out of 12 spaces
                // on medium or larger devices  
                <Grid item xs={12} md={4} >
                    // the first Grid is going to contain our list component 
                    <List /> 
                </Grid>
                // the map is much larger when compared to the list so md={8} on medium and 
                // larger devices
                <Grid item xs={12} md={8} >
                    // the second Grid is going to contain our map
                    <Map />
                </Grid>
            </Grid>
        </>
    );
}
export default App;
```
--------------------------------------------------------------------------------------------

#### Header.jsx
```jsx
import React from 'react';
// this autocomplete is coming from react-google-maps
import { Autocomplete } from '@react-google-maps/api';
// things from material ui
import { AppBar, Toolbar, Typography, InputBase, Box } from '@material-ui/core';
import SearchIcon from '@material-ui/icons/Search';


const Header = () => {
    return (
        // position in AppBar is static
        <AppBar position='static'>
            <Toolbar className={classes.toolbar}>
                // Typography is simply every single text element but you can change variant
                // you can make it heading subtitles and titles
                <Typography variant='h5' className={classes.title} > 
                    Travel Advisor
                </Typography>
                // in material ui a box is basically a div
                // but u can simply set a display of flex and some other property
                <Box display='flex'>
                    // inside of it we want to have one more Typography
                    <Typography variant='h6' className={classes.title} > 
                        Explore new places
                    </Typography>
                    <Autocomplete>
                        // our search box
                        <div className={classes.search}>
                            <div className={classes.searchIcon}>
                                <SearchIcon />
                                // first we're going to set our root class then input class 
                                <InputBase 
                                    placeholder='Search...' 
                                    classes={{ root:classes.inputRoot,
                                                input: classes.inputInput }} 
                                />
                            </div>
                        </div>
                    </Autocomplete>
                </Box>
            </Toolbar>
        </AppBar>
    );
}
export default Header;
```

#### so how do the classes work in material ui 
#### style.js
```js
import {makeStyles } from '@material-ui/core/styles'

// we can call this function, and then inside of there it requires one more callback function
// the callback function inside returns an object, so we have to wrap it in parentheses
export default makeStyles(() => ({
    // whatever styles u want to add, you can simply add them in css in js type of way 
    // that means if you want to have a class name title, you would create a title property
    // that's going to be an object
    title: {
        // it's kind of clumsy to write css in this way
        color: 'red'
    }
}))
```
#### style.js
```js
import { alpha, makeStyles } from '@material-ui/core/styles';

export default makeStyles((theme) => ({
  title: {
    display: 'none',
    [theme.breakpoints.up('sm')]: {
      display: 'block',
    },
  },
  search: {
    position: 'relative',
    borderRadius: theme.shape.borderRadius,
    backgroundColor: alpha(theme.palette.common.white, 0.15),
    '&:hover': { backgroundColor: alpha(theme.palette.common.white, 0.25) },
    marginRight: theme.spacing(2),
    marginLeft: 0,
    width: '100%',
    [theme.breakpoints.up('sm')]: { marginLeft: theme.spacing(3), width: 'auto' },
  },
  searchIcon: {
    padding: theme.spacing(0, 2), height: '100%', position: 'absolute', pointerEvents: 'none', display: 'flex', alignItems: 'center', justifyContent: 'center',
  },
  inputRoot: {
    color: 'inherit',
  },
  inputInput: {
    padding: theme.spacing(1, 1, 1, 0), paddingLeft: `calc(1em + ${theme.spacing(4)}px)`, transition: theme.transitions.create('width'), width: '100%', [theme.breakpoints.up('md')]: { width: '20ch' },
  },
  toolbar: {
    display: 'flex', justifyContent: 'space-between',
  },
}));
```
#### back Header.jsx  import useStyles from './styles';
#### just by looking ad the name, you can notice that the useStyles seems to be a hook
``` jsx
import useStyles from './styles'

const Header = () =>{
    const classes = useStyles();
}
// now we have done layout and styles
```
------------------------------------------------------------------------------------------
#### Map
```jsx
import React from 'react';
import GoogleMapReact from 'google-map-react';
import { Paper, Typography, useMediaQuery } from '@material-ui/core';
// Paper is basically a div with a background color
// useMediaQuery is help us with making our map more mobile responsive 

import LocationOutlinedIcon from '@material-ui/icons/LocationOutlined'; 

// import rating component 
// some of material ui components are still being worked on rating is one of them
// that is why we're not importing it from core, 
import Rating from '@material-ui/lab/Rating';

// import the classes as well
import useStyles from './styles';

const Map = () =>{
    const classes = useStyles();

    // we will useMediaQuery hook, we pass in a string, that string is going to 
    // have parentheses and then inside of there we can say  min-width:600px
    // means this is mobile variable is going to be set to false 
    // if the width of the device is larger than 600 pixels 
    const isMobile = useMediaQuery('(min-width: 600px)');

    // create coordinates , the property are latitude and longitude
    const coordinates = {lat: 0, lng : 0};

    return (
        <div className={classes.mapContainer}>

            <GoogleMapReact
                // first property is bootstrapURLKeys={{key: ''}}
                // inside of the object is key is equal to and now in here we can specify
                // the google maps key 
                // to get our key we'll have to create a new project on google developers console
                // we're going to do that as soon as we add all the other props required for this 
                // google map react
                bootstrapURLKeys={{key:''}}
                // the second property is going to be default center
                // so this is going to be the center of our map 
                // and in here we can pass the coordinates 
                // right we dont have real coordinates, so we create const coordinates
                // and in there we're going to pass the coordinates
                defaultCenter={coordinates}
                // real center, is the current center of the map 
                // and in there we can also pass the coordinates
                center = {coordinates}
                // we can also setup the default zoom in this case 14 to be working 
                defaultZoom = {14}
                // finally we're gonna have some margins
                // it accepts an array, it has four different properties top right bottom left
                margin={[50,50,50,50]}
                // we're gonna have three more properties which we're gonna left empty for now
                // one of these is options
                options={}
                // onChange property which is gonna be really important when you change the map
                onChange={}
                // onChildClick property which is going to be used when you actually click on 
                // a restaurant on the map
                onChildClick={}
                // right now for this to work these must not be empty, it's going to put 
                // an empty string in there 
                // these are all the props that we need for our google map react
            >
            </GoogleMapReact>
        </div>
    );

}
export default Map;
```

#### get that api key   
> console.cloud.google.com/projectcreate
> 
> put the api key in bootstrapURLKeys={{key:''}}


-------------------------------------------------------------------------------------------
#### create the layout for the list and start working on the functionality of fetching all the restaurants hotels and attractions
#### List
```jsx
import React,{useState} from 'react'
import {CircularProgress, Grid, Typography, InputLabel, MenuItem, FormControl, Select} from '@material-ui/core'
// CircularProgress is material ui loading bar
import useStyles from './styles'

const List = () => {
    const classes = useStyles();

    const [type, setType] = useState('restaurants');
    const [rating, setRating] = useState('');

    return (
        <div className={classes.container}>
            <Typography variant='h4'>Restaurants, Hotels & Attractions around you</Typography>
            <FormControl className={classes.formControl}>
                <InputLabel>Type</InputLabel>
                <Select value={type} onChange={(e) => setType(e.target.value)}>
                    //inside of select we're going to have a menuItem
                    <MenuItem value='restaurants'>Restaurants</MenuItem>
                    <MenuItem value='hotels'>Hotels</MenuItem>
                    <MenuItem value='attractions'>Attractions</MenuItem>
                    // these data have to be modified useState
                </Select>
                <Select value={rating} onChange={(e) => setRating(e.target.value)}>
                    <MenuItem value={0}>All</MenuItem>
                    <MenuItem value={3}>Above 3.0</MenuItem>
                    <MenuItem value={4}>Above 4.0</MenuItem>
                    <MenuItem value={4.5}>Above 4.5</MenuItem>
                </Select>
            </FormControl>
        </div>
    );
}
```
#### now we have to start rendering through the list of restaurants 
#### and then displaying a card for each specific place
#### so far we haven't yet fetched restaurants hotels 
```jsx
import React,{useState} from 'react'
import {CircularProgress, Grid, Typography, InputLabel, MenuItem, FormControl, Select} from '@material-ui/core'

import PlaceDetails from '../PlaceDetails/PlaceDetails'

import useStyles from './styles'

const List = () => {
    const classes = useStyles();
    const [type, setType] = useState('restaurants');
    const [rating, setRating] = useState('');

    // create a variable const places which is going to be a dummy variable for now
    // in ther we can simply say const place and let's say that each place is an object
    // and it's going to have a name property
    const places = [
        {name:'Cool Place'},
        {name:'Best Beer'},
        {name:'Best Steak'},
    ] 
    return (
        <div className={classes.container}>
            <Typography variant='h4'>Restaurants, Hotels & Attractions around you</Typography>
            <FormControl className={classes.formControl}>
                <InputLabel>Type</InputLabel>
                <Select value={type} onChange={(e) => setType(e.target.value)}>
                    <MenuItem value='restaurants'>Restaurants</MenuItem>
                    <MenuItem value='hotels'>Hotels</MenuItem>
                    <MenuItem value='attractions'>Attractions</MenuItem>
                </Select>
                <Select value={rating} onChange={(e) => setRating(e.target.value)}>
                    <MenuItem value={0}>All</MenuItem>
                    <MenuItem value={3}>Above 3.0</MenuItem>
                    <MenuItem value={4}>Above 4.0</MenuItem>
                    <MenuItem value={4.5}>Above 4.5</MenuItem>
                </Select>
            </FormControl>
 
            <Grid container spacing={3} className={classes.list}>
                // inside of there, we can open a block of code
                //{places?.} means only if you have places only then map
                //take in a callback function and then each iteration of the callback
                //it has one new place, so if we're mapping our places we can say in here
                //we're also going to need the index and index is always given to you 
                //as the second parameter to the map function 
                //we need curly braces if you really want to open this as a function 
                //but in this case we only need parentheses because we're going to 
                //instantly return a piece of jsx
                {places?.map((place, i) =>(
                    //for each we want to return a Grid, but this time not a grid container
                    //rather Grid item, that Grid item is going to have a key equal to index
                    //xs={12} means extra small devices all the way the big devices
                    //they're gonna take the full width of the list container which is 12 spaces
                    <Grid item key={i} xs={12}>
                        // inside of Grid is where our card should be
                        // a Card is yet another component that is a 
                        // custom component we need to create, that is the place details 
                        // pass into it is going to be a place 
                        <PlaceDetails place={place} />
                    </Grid>
                ))}
            </Grid>
        </div>
    );
}
```
# PlaceDetails.jsx
```jsx
import React from 'react'

const PlaceDetails =({ place }) => {
    return(
        // each place have name, so lets render it for each one
        <h1>{place.name}</h1>
    );
}
export default PlaceDetails;
```