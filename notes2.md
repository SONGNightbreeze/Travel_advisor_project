### Working with API
#### api/index.js
> this is where we're going to keep all of our api calls, showcase, central, entire visible area of the map, 
```js
import axios from 'axios'

const getPlacesData = async () =>{  
    try{
        const response = await axios.get();
        // means url we're going to make our api 
    } catch(error){

    }
}
```
### rapidaapi.com/hub  -->search travel advisor 

> we dont want to see the restaurants just around this central point 
> 
> we want to see it in our entire visible area of the map that's why we're going to use list in boundary
> 
> in this case you have to pass the latitude and longitude of the bottom
> left corner of the map and also the top right corner latitude and longitude
>
> so we have to find the position of the bottom left corner and the position of 
> the top right corner, if we do that the restaurants that we fetch are going to be
> scattered across our entire map

```js
import axios from 'axios';

// store url in a variable
const URL= 'https://travel-advisor.p.rapidapi.com/restaurants/list-in-boundary'

const options={
    // since we're using axios the method is automatically going to be set to GET
    // so we dont have to use that 
    //method: 'GET',
    //url:'https://travel-advisor.p.rapidapi.com/restaurants/list-in-boundary',
    
    // params, we definitely need the bottom left latitude and longitude
    // and top right latitude and longitude 
    // but we dont need any other options, so delete them 
    params: {
        bl_latitude: '11.847676',
        bl_longitude: '12.838442',
        tr_longitude: '109.095887',
        tr_latitude: '109.149359',
    },
    // then we also need the headers, this is where your rapid api key resides
    // that means we'll alse have to exchange this to environment variables later
    headers: {
        'x-rapidapi-key': '',  //this is where your rapid api key resides
        'x-rapidapi-host': 'travel-advisor.p.rapidapi.com',
    }
};

export const getPlacesData = async() => {
    try{
        // the second parameter to the url are the options so you can simply
        // pass the options object right inside of here

        // we could return the response but we're not really interested in the response
        // we're more interested in what's inside of the response, so that going to be data
        // so we can destructure the data right in here and then one more time we need to 
        // destructure the data from inside to get to our restaurants,finally we can return data
        const {data: {data}} = await axios.get(URL, options);

        return data
        
    } catch (error){
        // we're simply going to cosole.log the error
        console.log(error)
    }
}
```
### the main question is where are we going to call this funciton 
### the answer is inside of the App.js 
```js
import React, {useState, useEffect} from 'react'
import { CssBaseline, Grid } from '@material-ui/core'

import { getPlacesData } from './api'
import Header from './components/Header/Header'
import List from './components/List/List'
import Map from './components/Map/Map'


// so now the question is how can we call this function inside of our functional
// app component, the answer is we are going to 'create a useEffect' 
const App = () => {

    const [places, setPlaces] = useState([]);

    useEffect(() => {
        //we're going simply to call it as a function 
        getPlacesData()
        // dont forget this function returns something 
        // more specifically it returns the restaurants' data
        // so set useState
        // we also have to remember that our get places data is Async function
        // that means we have to call .then
            .then((data) => {
                // just we can see if it's being fetched properly
                console.log(data)
                setPlacecs(data)
            })
    }, []); 
    // dependency empty array

    return (
        <>
            <CssBaseline />
            <Header />
            <Grid container spacing={3} style={{ width:'100%' }}> 
                <Grid item xs={12} md={4} > 
                    <List />  
                </Grid>
                <Grid item xs={12} md={8} >
                    <Map />
                </Grid>
            </Grid>
        </>
    );
}
export default App;

```
> now we have the function making a request 
> 
> everything is static, we dont have real coordinates here 
>
> but we're going to make this work really soon 
> 
> we just going to see if it's going to work with static coordinates

> all of these are restaurants
> every restaurant has a lot of information like an address object with the entire city street
> and country, it has different categories different types of cuisines
> and a lot of information that we can use to display on the map and on your cards
> that means that we are fetching the data fro the api correctly 
--------------------------------------------------------------------------------

### we make this dynamic 
### we have to get real information from the position of our map
### then based on that we have to call only the right restaurants for that map

#### App.js
```js
import React, {useState, useEffect} from 'react'
import { CssBaseline, Grid } from '@material-ui/core'

import { getPlacesData } from './api'
import Header from './components/Header/Header'
import List from './components/List/List'
import Map from './components/Map/Map'

const App = () => {

    const [places, setPlaces] = useState([]);

    // we'll have to pass some information into this get places data function 
    // so create more useState fields more specifically 
    const [coordinates, setCoordinates] = useState({});
    // remember the top right and the bottom left corner we're going to call them bounds
    const [bounds, setBounds] = useState(null)
    // now we have data but it's not populated yes these are just empty values 
    
    useEffect(() => { 
        getPlacesData();       
                console.log(data)
                setPlacecs(data)
            })
    }, []); 

    return (
        <>
            <CssBaseline />
            <Header />
            <Grid container spacing={3} style={{ width:'100%' }}> 
                <Grid item xs={12} md={4} > 
                    <List />  
                </Grid>
                <Grid item xs={12} md={8} >
                    // so we have to pass the setter function as props to our Map
                    // we're going to pass the setCoordiantes prop which is goint to be
                    // equal to that setter function of setCoordinate
                    <Map 
                        setCoordinates = {setCoordinates}
                        setBounds = {setBounds}
                        // our Map is going to have to use these coordinates 
                        // so lets also pass the state itself coordinates 
                        coordinates = {coordinates}
                    />
                </Grid>
            </Grid>
        </>
    );
}
export default App;

```
#### Map.jsx   
```jsx
// receive different props
const Map = ({ setCoordinates, setBounds, coordinates }) ={
    // as you can see before we even declared empty coordinates 
    // but we're no longer going to need that 
    // so im simply going to delete it const coordinates = {lat:0, lng:0}
}
```
### now the question is how are we going to know when the coordinates or bounds of the Map change 
### the answer is google Map react will be doing that for us 
### we simply have to call a specific onChange function 
```jsx
import React from 'react';
import GoogleMapReact from 'google-map-react';
import { Paper, Typography, useMediaQuery } from '@material-ui/core';
import LocationOutlinedIcon from '@material-ui/icons/LocationOutlined'; 
import Rating from '@material-ui/lab';

import useStyles from './styles';

const Map = () =>{
    const classes = useStyles();
    const isMobile = useMediaQuery('(min-width: 600px)');


    return (
        <div className={classes.mapContainer}>

            <GoogleMapReact
                bootstrapURLKeys={{key:''}}
                defaultCenter={coordinates}
                center = {coordinates}
                defaultZoom = {14}
                margin={[50,50,50,50]}
                options={}
                // inside of onChange we're getting a callback function has a event inside of it
                onChange={(e) => {
                    console.log(e) 
                    // we get the bounds, center, marginBounds, size, zoom properties
                    // we only need the center and the bounds, 
                    // in the center we can see lat:0 lng:0,  in the bouds, ne nw se sw
                    // these bounds are going to be really helpful with setting our top right
                    // and bottom left corners

                    // inside of there we are going to set it to an object
                    // object that has the latitude property which is going to be equal to 
                    // e.center.lat, also lng: e.center.lng
                    setCoordiantes({lat: e.center.lat, lng:e.center.lng })
                    setBounds({ne:e.center.lat, lng: e.center.lng})
                }}
                onChildClick={}
            >

            </GoogleMapReact>
        </div>
    );

}
// this is all that we have to do to successfully populate these variables
```
#### App.jsx   so now we go back inside of our App
```js
const [coordinates, setCoordinates] = useState({lat:0, lng:0})

useEffect(() => {
    // if we console log this variable right there
    console.log(coordinates, bounds)
    // they are going to be change when we change the Map, 
    // before we check out,lets properly set these coordinates 
    // at the default {lat: 0, lng:0}

    getPlacesData()
        .then((data) => {

        })
}, [coordinates, bounds])
```

> i want to automatically set the coordinates to be the coordinates of the user's location
>
> as as soon as the user launches the page, we should be able to get their latitude and longitude
> 
> we can do that by using one more useEffect this useEffect is only going to happen at that start 
```js
useEffect(() => {
    // at the start to get the user coordinates we can use the built-in
    // browser geolocation api 
    // we can say navigator.geolocation.getCurrentPosition()
    // and then in there we're gonna have a callback function 
    // we can choose what do you want to do with that data 
    // we can destructure it to get the coordinates to get latitude, longitude
    navigator.geolocation.getCurrentPosition(({coords: {latitude, longitude}}) => {
        // now we're getting data and setCoordinates 
        setCoordinates({lat: latitude, lng:longitude});
        // finally we're getting the coordinates, we no longer have to 
        // set these default value right there 
        // we can simply leave this as an empty object
    })
}, [])
```

### we already have the coordinates and the bounds,
### bounds is more important in this case, we simply need to pass them over to get places data
```js
useEffect(() => {
    // so at the first parameter we can pass the bound southwest and bound northeast
    // now we're passing that over the function 
    getPlacesData(bounds.sw, bounds.ne)
        .then((data) => {
            console.log(data);

            setPlaces(data);
        })
}, [coordinates, bounds]);

```

### we can go back the api and now we can accept those bounds right there
#### api/index.js
```js
export const getPlacesData = async (sw, ne) => {
    try {
        const {data: {data}} = await axios.get(URL, options);
        return data
    } catch(error){
        console.log(error)
    }
}

```
### so how can we actually pass these values to our api request 
### copy this entire object remove it from here,
### and then simply paste it right here as the second parameter of our api call
```js
export const getPlacesData = async (sw, ne) => {
    try {
        const {data: {data}} = await axios.get(URL, {
            // now we can remove all of these values which is not true
            // these are just static and we can simply use our southwest and northeast
            // properties to get the real bounds
            params: {
                // bl = bottom left is going to be southwest
                // tr = top right is goint to be northeast 
                bl_latitude: sw.lat,
                bl_longitude: ne.lat,
                tr_longitude: sw.lng,
                tr_latitude: ne.lng,
            },
            // we are also passing the headers 
            headers: {
                'x-rapidapi-key': '',
                'x-rapidapi-host': 'travel-advisor.p.rapidapi.com',
            }  
        });
        // now the data that's being returned should be inside of our map
        // let's check it out in the browser 
        // as you can see, we got 33 restaurants in this case 
        // and this address seems to be correct as you can see
        return data
    } catch(error){
        console.log(error)
    }
}
```

> we are indeed fetching correct restaurants and now we can start displaying them either
> 
> on the map or we can start with the list, considering we already started with a list
> 
> lets go ahead and implement that nice looking card and we'll be able to see restaurants in no time


#### App.js   pass places to the List
```js
const App = () => {
    return (
        <>
            <CssBaseline />
            <Header />
            <Grid container spacing={3} style={{ width:'100%' }}>
                <Grid item xs={12} md={4} >
                    // pass the places
                    <List places={places} />  
                </Grid>
                <Grid item xs={12} md={8} >
                    <Map 
                        setCoordinates = {setCoordinates}
                        setBounds = {setBounds}
                        coordinates = {coordinates}
                    />
                </Grid>

            </Grid>

        </>
```
#### List
```js
const List = ({places}) => {}
```