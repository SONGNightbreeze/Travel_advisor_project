### Scroll Places & Filters

> now we're displaying these places on the map, let's make them clickable
> that's exactly where this onChildClick() method is going in handy
> onChildClick() is an event listener
# Map.jsx
```jsx
const Map =({ setCoordinates, setBounds, coordinates, places }) ={

    const classes = useStyles()
    const isDesktop = useMediaQuery('(min-width:600px)')


    return (
        <div className={classes.mapContainer}>

            <GoogleMapReact
                bootstrapURLKeys={{key:''}}
                defaultCenter={coordinates}
                center = {coordinates}
                defaultZoom = {14}
                margin={[50,50,50,50]}
                options={}
                onChange={(e) => {
                    console.log(e) 
                    setCoordiantes({lat: e.center.lat, lng:e.center.lng })
                    setBounds({ne:e.center.lat, lng: e.center.lng})
                }}
                //is an event listener, so we're going to have method to listen for our events
                //whenever we click on a child, we're going to get it right here as a parameter
                //and then what do we want to do with it, we somehow have to get the information
                //about which child is click from the map component all the way 
                //to our list component and how can we do that
                //we can create a child and setChild click field right here a state for example
                //const [childClicked, setChildClicked] = useState(null)
                //but now we want take that state to the parent component of 
                //both the list and the map so in this case, that's going to be App component, 
                //inside of here,  get that state childClicked, you could have solved this 
                //problem by useing context or redux, but we want to keep in simple
                onChildClick={(child) => {

                }}
            >

            </GiigleMapReact>
        </div>
}
```
# App
```js
const App = () =>{
    const [childClicked, setChildClicked] = useState(null);
}
return (
    <>
        <CssBaseline />
        <Header />
        <Grid container spacing={3} style= {{width:'100%'}}>
            <Grid item xs={12} md={4}>
                <List 
                    places={places} 

                    // when we call that function the childClicked variable is going to
                    // be populated and now we can pass it over to the list
                    childClicked = {childClicked}
                />
            </Grid>
            <Grid item xs={12} md={8}>
                <Map
                    setCoordinates={setCoordinates}
                    setBounds={setBounds}
                    coordinates={coordinates}
                    places={places}                

                    // let's pass this method to the map
                    setChildClicked = {setChildClicked}
                />
            </Grid>
        </Grid>
    </>
)
```
#### back to the Map
```js
const Map =({ setCoordinates, setBounds, coordinates, places, setChildClicked }) ={
// ......
    return (
        <div className={classes.mapContainer}>

            <GoogleMapReact
                bootstrapURLKeys={{key:''}}
                defaultCenter={coordinates}
                center = {coordinates}
                defaultZoom = {14}
                margin={[50,50,50,50]}
                options={}
                onChange={(e) => {
                    console.log(e) 
                    setCoordiantes({lat: e.center.lat, lng:e.center.lng })
                    setBounds({ne:e.center.lat, lng: e.center.lng})
                }}
                onChildClick={(child) => setChildClicked(child)}
            >
            </GoogleMapReact>
        </div>
```
#### List
```jsx

const List =({places, childClicked}) ={
    console.log({childClicked})
    // put the console inside of the childClicked 
    // if we put the property inside of console.log it's going to give more information
    // if we did console.log just like this 
    console.log(childClicked)
    // for example the value is 5, if you had a lot of console.logs, you wouldn't know
    // what this 5 stands for, it's going to give an object where is going to say 
    // childClicked is equal to 5  {childClicked : 5} that way you know exactly what you cons
}
```

> how do we make our list scroll to a specific element on the list when you click on the map
> for that we're gonna to have to useRefs
#### List
```jsx
import React, {useState, useEffect, createRef } from 'react'
import {CircularProgress, Grid, Typography, InputLabel, MenuItem, FormControl, Select} from '@material-ui/core'

import PlaceDetails from '../PlaceDetails/PlaceDetails'

import useStyles from './styles'

const List = (places, childClicked) => {
    const classes = useStyles();
    const [type, setType] = useState('restaurants');
    const [rating, setRating] = useState('');

    //console.log({ childClicked })

    // we want to create a state field that's going to contain all the references
    // so we can say const and let's call it something like elRefs element references
    const [elRefs, setElRefs] = useState([])
    // at the start set an empty array, because at the start we dont have any places
    // once we get the places we want to set them to the state

    useEffect(() => {
        // how exactly are we going to create these references 
        // we can do it like this const refs = Array() we use the array constructor
        // and then inside of there we want to construct as many as elements
        // as there are places so in there we pass Array(places.length)
        // then we're going to call the .fill() is going to start filling the array
        // and then we want to .map() over that array
        // .map(() => ) this time we are not interested in one specific thing at the
        // start we only need index in cases like these .map((_,i) => )
        // put the underscore that means you're not going to use that first parameter
        // but you need second one, and we want to return    => refs[i]
        // so we want to access the refs and return that specific thing
        // or if the ref doesn't exist yet in that case we can create a new ref createRef() 
        const refs = Array(places?.length).fill().map((_, i) => refs[i] || createRef());
        // and this refs here is not gonna be like that, because we are still declaring it
        // in that line we have to use the refs right there

        // usually you have to create one ref, in this case we really have to create as many as
        // there are places
        // finally we have those refs we can setElRefs is equal to refs
        setElRefs(refs)
        // now we can actually use those references right below here in place details 
        // 下面的<Grid>的内容
    
    },[places])

    return (
        <div className={classes.container}>
            <Typography variant='h4'>Restaurants, Hotels & Attractions around you</Typography>
            <FormControl calssName={classes.formControl}>
                <InputLabel>Type</InputLabel>                
                <Select value={type} onChange={(e) => setType(e.target.value)}>
                    <MenuItem value='restaurants'>Restaurants</MenuItem>
                    <MenuItem value='hotels'>Hotels</MenuItem>
                    <MenuItem value='attractions'>Attractions</MenuItem>
                </Select>
            </FormContrl>
            <FormControl calssName={classes.formControl}>
                <Select value={rating} onChange={(e) => setRating(e.target.value)}>
                    <MenuItem value={0}>All</MenuItem>
                    <MenuItem value={3}>Above 3.0</MenuItem>
                    <MenuItem value={4}>Above 4.0</MenuItem>
                    <MenuItem value={4.5}>Above 4.5</MenuItem>
                </Select>
            </FormControl>
            <Grid container spacing={3} className={classes.list} >
                {places?.map((place, i) => (
                    // so we're going to give each Grid item a ref in this case
                    // that's going to ref = {elRefs[i]} 
                    // access that specific index of the current place
                    <Grid ref={elRefs[i]}  item key={i} xs={12}>
                        <PlaceDetails
                            place={place}  
                            // then we have to pass few properties(props) to place details
                            // remember what we talked about once we click on this element 
                            // we want to scroll to that specific restaurant on the list
                            // so we're going to set the select property is this restaurant
                            // selected and how can we know that if the childClicked is 
                            // equal to index 
                            selected= {Number(childClicked) = i }
                            // if that's the case that means that this place details
                            // has been selected, this childClicked is a string though
                            // so we have to convert it into a number before we test it

                            // and we're also going to pass that ref by saying ref prop
                            // is equal to in this case elRefs[i] so the same we did above
                            refProp={elRefs[i]}
                            //we might not even need it later on in here <Grid ref={elRefs[i]}>
                        />
                    </Grid>
                ))}
            </Grid>
        </div>
    );
}
```
#### and now we passing the selected and the ref prop to the place details so let's use at PlaceDetails
```jsx
import React from 'react'
import { Box, Typography, Button, Card, CardMedia, CardContent, CardActions, Chip} from '@material-ui/core'

import LocationOnIcon from '@material-ui/icons/LocationOn' 
import PhoneIcon from '@material-ui/icons/Phone'

import Rating from '@material-ui/lab/rating'

import useStyles from './styles'

// 从List传入过来的 selected 和 refProp
const PlaceDetails = ({place, selected, refProp}) => {
    const classes = useStyles();

    //we're going to use this to make a simple check we're gonna to say if selected
    //in that case we want to call that refProp?. each ref has the current property
    //?.current?.scollIntoView() that's built in method on the html element
    //we're gonna put an object for options and we can say behavior is equal to a string 
    //of smooth and block is going to be set to a string of start
    if(selected) refProp?.current?.scrollIntoView({behavior:'smooth', block:'start'})
    // now back the list, we dont need refProp={elRefs[i]} the list in <Grid> in List
    // we're using it(refProp={elRefs[i]})  right inside of the place details

    return (
        <Card elevation={6}>
            <CardMedia
                style={{height: 350}}
                image={place.photo? place.photo.images.large.url: ...}

                title={place.name}
            />
            <CardContent>
                <Typography gutterBottom>{place.name}</Typography>
                <Box display='flex' justifyContent='space-between'>
                    <Typography variant='subtitle1'>Price</Typography>
                    <Typography gutterBottom variant='subtitle1'>{place.price_level}</Typography>
                </Box>
                <Box display='flex' justifyContent='space-between'>
                    <Typography variant='subtitle1'>Ranking</Typography>
                    <Typography gutterBottom variant='subtitle1'>{place.ranking}</Typography>
                </Box>
                {place?.awards?.map(() => (
                    <Box  my={1} display='flex' justifyContent='space-between' alignItems='center'>
                        <img src={award.images.small} alt={award.display_name} />
                        <Typography variant='subtitle2' color='textSecondary'>{award.display_name}</Typography>
                    </Box>
                ))}
                {place?.cuisine?.map(({ name }) => (
                    // for each one, we want to show a chip component
                    <Chip key={name} size='small' label={name} className={classes.chip} />
                ))} 
            </CardContent>   
        </Card>
    )
}

```
> TypeError: Cannot read property 'length' of undefined 
> means dont understand places.length
> and therefore we cannot create refs, so this is right opportunity to 
> implement our circular progress in List
#### so let's go inside of App component and then in here let's create a new state loading
```js
const App = () => {
    
    //......

    const [isLoading, setIsLoading] = useState(false);

    //......

    useEffect(() => {
        // then right before we call the get places data, im going to set it to true
        setIsLoading(true)

        getPlacesData(bounds.sw, bounds.ne)
            .then((data) => {
                setPlaces(data);
                // finally after we're done fetching the data right here
                // setIsLoading back to false 
                setIsLoading(false)         
            })
    }, [coordinates, bounds])
    return (
    <>
        <CssBaseline />
        <Header />
        <Grid container spacing={3} style= {{width:'100%'}}>
            <Grid item xs={12} md={4}>
                <List 
                    places={places} 
                    childClicked={childClicked}
                    //now we have this state we can pass state over to the list 
                    isLoading={isLoading}
                />
            </Grid>
            <Grid item xs={12} md={8}>
                <Map
                    setCoordinates={setCoordinates}
                    setBounds={setBounds}
                    coordinates={coordinates}
                    places={places}
                    setChildClicked={setChildClicked}
                />
            </Grid>
        </Grid>
    </>
)
}
```
#### back to the List
```js
const List = ({places, childClicked, isLoading}){
    //...
}
    return(
        <div className={classes.container}>
            <Typography variant='h4'>Restaurants, Hotels & Attractions around you</Typography>
            // and just below this typography we're going to add a logical block 
            // saying isLoading
            {isLoading?(
                <div className={classes.loading}>
                    // inside of there we're going to have a CircularProgress
                    // which is going to have the size = '5rem'
                    <CircularProgress size='5rem' /> 
                </div>
            ) : ( 
            <>
            <FormControl calssName={classes.formControl}>
                <InputLabel>Type</InputLabel>                
                <Select value={type} onChange={(e) => setType(e.target.value)}>
                    <MenuItem value='restaurants'>Restaurants</MenuItem>
                    <MenuItem value='hotels'>Hotels</MenuItem>
                    <MenuItem value='attractions'>Attractions</MenuItem>
                </Select>
            </FormContrl>
            <FormControl calssName={classes.formControl}>
                <Select value={rating} onChange={(e) => setRating(e.target.value)}>
                    <MenuItem value={0}>All</MenuItem>
                    <MenuItem value={3}>Above 3.0</MenuItem>
                    <MenuItem value={4}>Above 4.0</MenuItem>
                    <MenuItem value={4.5}>Above 4.5</MenuItem>
                </Select>
            </FormControl>
            <Grid container spacing={3} className={classes.list} >
                {places?.map((place, i) => (
                    <Grid ref={elRefs[i]}  item key={i} xs={12}>
                        <PlaceDetails
                            place={place}  
                            selected= {childClicked = i }
                            refProp={elRefs[i]}
                        />
                    </Grid>
                ))}
            </Grid>
            </>
            )}
        </div>
    )
```
#### the scroll is finally done 
#### the thing that would make sense to do next are these select elements which
#### we've nicely created we need to be able to toggle between restaurants hotels
#### and attractions and we need to be able to filter out restaurants hotels and attractions by rating
-----------------------------------------------------------------------------------------

#### to start filtering our places we can visit the source and then components and then finally List 
```jsx
import React, {useState, useEffect, createRef } from 'react'
import {CircularProgress, Grid, Typography, InputLabel, MenuItem, FormControl, Select} from '@material-ui/core'

import PlaceDetails from '../PlaceDetails/PlaceDetails'

import useStyles from './styles'

const List = (places, childClicked, isLoading) => {
    const classes = useStyles();
    // inside of here, we created that type state and also the rating state
    // now we're going to use the same method we used just a few minutes ago
    // we're going to lift the state up, move it into the App.js
    //const [type, setType] = useState('restaurants');
    //const [rating, setRating] = useState('');


```
# App.js
```js
const App = () => {
    const [places, setPlaces] = useState([])
    const [childClicked, setChildClicked] = useState(null)

    const [coordinates, setCoordinates] = useState({})
    const [bounds, setBounds] = useState({})

    const [isLoading, setIsLoading] = useState(false)

    const [type, setType] = useState('restaurant')
    const [rating, setRating] = useState('')
    // and then pass these state to the List as property
}
    // ....
        return (
    <>
        <CssBaseline />
        <Header />
        <Grid container spacing={3} style= {{width:'100%'}}>
            <Grid item xs={12} md={4}>
                <List 
                    places={places} 
                    childClicked={childClicked}
                    isLoading={isLoading}
                    // pass the state to our list
                    type={type}
                    setType={setType}
                    rating={rating}
                    setRating={setRating}
                    // we're declaring everything in our App and then we are passing it
                    // into our list, as you can see, we're creating a lot of states 
                    // inside of our app and then we're passing everything as props
                    // through our components once again this is not best practice
                    // but in this case it's just fine, because we're passing it one
                    // level down only to the list, but if we had one more component
                    // inside of the list which we needed to pass those props again
                    // from the list to that component or even deeper in that case
                    // i would strongly advise you to use react context
                    // but in this case, this is just fine
                />
            </Grid>
            <Grid item xs={12} md={8}>
                <Map
                    setCoordinates={setCoordinates}
                    setBounds={setBounds}
                    coordinates={coordinates}
                    places={places}
                    setChildClicked={setChildClicked}
                />
            </Grid>
        </Grid>
    </>
)
}
```
#### so back to the list and let's get these things from props 
#### List
```jsx
const List = (places, childClicked, isLoading, type, setType, rating, setRating ) => {
}
```

#### the only reason why we're doing this, now in the App we have access to the rating 
#### and to the types and now we can use them to get different data from our get places api
#### App.js
```js
useEffect(() => {
    setIsLoading(true);

    // so as the first parameter to our get places data even before the bounce
    // we're going to pass over the type, that way our function is going to know 
    // what data it needs to fetch 
    getPlacesData(bounds.sw, bounds.ne)
        .then((data) => {
            setPlaces(data);
            setIsLoading(false);
        })
}, [coordinates, bounds])
```
#### so let's go to our get places data
#### src/api/index.js
```js
import axios from 'axios'
// then right inside of here we are going to copy this url
// instead of here this static variable 取消变量，直接将地址放到axios中用``包裹
//const URL= 'https://travel-advisor.p.rapidapi.com/restaurants/list-in-boundary'
export const getPlacesData = async (sw, ne) => {
    try {
        // as you can see, we have restaurants, but in this case to make it dynamic
        // we're simply going to get that type that we're receiving through the parameters
        // that way if we have a hotels as type, we get hotels if we have attractions ...
        const {data: {data}} = await axios.get(`https://travel-advisor.p.rapidapi.com/${type}/list-in-boundary`, {
            params: {
                bl_latitude: sw.lat,
                bl_longitude: ne.lat,
                tr_longitude: sw.lng,
                tr_latitude: ne.lng,
            },
            headers: {
                'x-rapidapi-key': '',
                'x-rapidapi-host': 'travel-advisor.p.rapidapi.com',
            }  
        });

        return data
    } catch(error){
        console.log(error)
    }
}
```
#### this simple change make our code dynamic for this function in the List reran when the type changes, we have to add type to the dependencies array in App
```jsx
useEffect(() => {
    setIsLoading(true);

    getPlacesData(bounds.sw, bounds.ne)
        .then((data) => {
            setPlaces(data);
            setIsLoading(false);
        })
}, [type, coordinates, bounds])
```

#### next step is to fix the filtering based on the rating  
#### App.js
```js

    const [filteredPlaces, setFilteredPlaces] = useState([]);

    // we're going to add one more useEffect and you might be thinking 
    // wait is it even legal to have so many useEffects and the answer is yes
    // but every single one of them needs to serve a different purpose 
    // this one is going to change only rating changes 
    // as you can see each useEffect serves a specific purpose     
    useEffect(() => {
        // the only thing we want to do in here 
        // in this case we get a place and we're gonna say if place.rating is larger
        // than the current rating 
        // in this case,we want to return that specific place that means if the rating
        // of the place is larger of the currently selected rating then we want to 
        // return that place, finally we want to set these places to a new state
        // just at the top const [filteredPlaces, setFilteredPlaces] = useState([]);
        const filteredPlaces = places.filter((place) => place.rating > rating)

        //we're going to call it and pass in the filtered places variable
        setFilteredPlaces(filteredPlaces);

    }, [rating])

    useEffect(() => {
        setIsLoading(true);

        getPlacesData(bounds.sw, bounds.ne)
            .then((data) => {
                setPlaces(data);
                //every time we get the new places data, we have to reset our filtered places
                //so let's say setFilteredPlaces back to empty array
                setFilteredPlaces([]); 
                setIsLoading(false);
            })
    }, [type, coordinates, bounds])
```

#### so now the question where are we going to use these filtered places
#### if we have any filters then we can pass them to our map and also to our list

#### App.js
```js


    return (
    <>
        <CssBaseline />
        <Header />
        <Grid container spacing={3} style= {{width:'100%'}}>
            <Grid item xs={12} md={4}>
                <List 
                    // we're already passing places so let's pass it right here we can say
                    // if we have filtered places so we can say if there is filtered places
                    // filteredPlaces.length in that case we want to pass the filter places
                    // but else we want to simply pass places 
                    places={filteredPlaces.length ? filteredPlaces : places} 
                    childClicked={childClicked}
                    isLoading={isLoading}
                    type={type}
                    setType={setType}
                    rating={rating}
                    setRating={setRating}
                />
            </Grid>
            <Grid item xs={12} md={8}>
                <Map
                    setCoordinates={setCoordinates}
                    setBounds={setBounds}
                    coordinates={coordinates}
                    places={filteredPlaces.length ? filteredPlaces : places}
                    setChildClicked={setChildClicked}
                />
            </Grid>
        </Grid>
    </>
)
}
```
> next step in making thia even more complete we already have a really really complex
> full-fledged travel advisor application, what else can we do our search is not working yet