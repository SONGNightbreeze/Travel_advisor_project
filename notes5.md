### Places Search
#### to implement places search we're going to revisit our header component
#### Header
```jsx
import React from 'react';
// this autocomplete is coming from react-google-maps for Search Places
import { Autocomplete } from '@react-google-maps/api';
import { AppBar, Toolbar, Typography, InputBase, Box } from '@material-ui/core';
import SearchIcon from '@material-ui/icons/Search';
import useStyles from './styles'

const Header = (setCoordinates) => {

    const classes= useStyles();

    // we're going to import useState from react, initialize a new state field
    const [autocomplete, setAutocomplete] = useState(null);
    // we want to set something to the state 
    // so what's going to happen onLoad, we're going to get that autocomplete
    // we can call it autoC and we're going to set that autocomplete right in here
    // so that we have it in the state and so that we can work with it 
    const onLoad = (autoC) => setAutocomplete(autoC)

    const onPlaceChanged = () =>{
        // we need to find the latitude and the longitude of the new location
        // if you're wondering how i got to this information you can find it online
        // or on google maps documentation 
        const lat = autocomplete.getPlace().geometry.location.lat()
        const lng = autocomplete.getPlace().geometry.location.lng()
        // the question is what do we want to do once we get our new place
        // we want to set it to the state more specifically 
        // we want to go back inside of the App and change coordinates
        // we can do that by passing the setCoordiante as a prop down to the header

        // inside of here we can call it setCoordinate we set it as an object 
        // and we specify the lat and the lng, this is all that we have to do 
        // with that specified our autocomplete should be almost done
        setCoordinates({ lat, lng });
    } 

    return (
        <AppBar position='static'>
            <Toolbar className={classes.toolbar}>
                <Typography variant='h5' className={classes.title} > 
                    Travel Advisor
                </Typography>
                <Box display='flex'>
                    <Typography variant='h6' className={classes.title} > 
                        Explore new places
                    </Typography>
                    // we have this autocomplete component 
                    // our <Autocomplete> requires two props that need to be passed 
                    // into it one thing is onLoad={} so what is going to happen 
                    // once we load the autocomplete component and then second thing
                    // is the on place changed handler and what's going to happen 
                    // once we change the place, so let's implement these two functions
                    // right inside of component

                    // if you take a look at this we are never actually connecting
                    // it to the google maps api, we didn't input any api key 
                    // or any such thing like that, with this @react-google-maps/api
                    // we have to do it using a script tag in the public index.html
                    <Autocomplete onLoad={onLoad} onPlaceChanged={onPlaceChanged} >
                        <div className={classes.search}>
                            <div className={classes.searchIcon}>
                                <SearchIcon />
                                <InputBase placeholder='Search...' classes={{ root:classes.inputRoot. input: classes.inputInput }} />
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
#### App
```js
    return(
        <>
            <CssBaseline />
            // we can do that by passing the setCoordiante as a prop down to the header
            <Header setCoordinates={serCoordinates} />
        </>
    )
```
> i'm going to give you the code for that script with all the other codes i gave you 
> so far so make sure to copy and past it right
> the only thing we have to change, it's going to be this api key
> key=AIzaSyDKAIWbkM0JwPKv5CVcIvM_iYsj5c7XtMs   right here 
> that api key has to be the same key that we are using inside of our Map component
> bootstrapURLKeys={{key:'AIzaSyDKAIWbkM0JwPKv5CVcIvM_iYsj5c7XtMs'}}  
```jsx
<script src="https://maps.googleapis.com/maps/api/js?v=3.exp&libraries=geometry,drawing,places&key=AIzaSyDKAIWbkM0JwPKv5CVcIvM_iYsj5c7XtMs"></script>
```

#### once we did that we also have to keep in mind that this is using the places api 
#### no longer just the regular google maps display a map 
#### we have to get all the places on earth
#### so we have to back to our google developers console  
#### console.cloud.goole.com/goole/maps-apis/api-list?project=traveladvisor-321518
#### and you can see that we're currently using only Maps JavaScript API we need to add
#### another Places API

--------------------------------------------------------------------------------
> let's try to go back to application and see everything is works'
> let's try searching for something like new york and if we click it 
#### we're instantly being teleported to new york, all of the restaruants on the map
#### but they quickly disappeared that means that switching the map works
#### but we still have some minor issues with fetching the restaurants depending on that
#### specific location, let's try to debug and fix that right away
#### i also noticed that there are some empty restaurants with no ratings
#### no name no image, so we need to be able to filter out those dummy restaurants
#### to do that we can go back to our App 
```js
    useEffect(() => {
        // it's important check is to only make this request if there are bounds
        // so just at the top, i'm going to say if bounds only then we want to do 
        // all of this code if we dont have the latitude the longitude the southwest
        // and the north east, we dont want to make this request  if(bounds) 
        if(bounds){
            setIsLoading(true)

            getPlacesData(type, bounds.sw, bounds.ne)
                .then((data) => {
                    // and then as soon as we get the data, 
                    // we need to filter that data to remove these things
                    // so right here inside of setPlaces we can add .filter()
                    // we get a specific place right there in the callback function
                    // and then we simply want to check is there the name 
                    // dose this place have a name if it does it also have the place. number
                    // of reviews is larger than zero if both of these conditions are true 
                    setPlaces(data?.filter((place) => place.name && place.num_reviews > 0));
                    setFilteredPlaces([]);
                    setIsLoading(false);
                })
        }
    }, [type, coordinates, bounds]);

```
#### and test it out in the browser there we can see all the best restaurants in London 
#### and finally let's switch to something like new york
#### it loaded all of them but for some reason it switched back to restaurants
#### in this list, but on the map we cannot see anything, let's inspect thie even further
#### by doing some console logs 

```js
    useEffect(() => {
        //...
    },[type, coordinates, bounds])

    // so i always want to know which ones are currently active places 
    // and also which are the currently active filtered places 
    console.log(places)
    console.log(filteredPlaces)
    // maybe that's causing the error
    
    // as you can see filtered places are empty which is fine
    // we can try switching this to about 3.0, now everything is there above 4.0 
    // still all of them are there and above 4.5 all of them are there 
    // because this API gives us only the best restaurants anyway
    // but this time these restaurants didn't disappear so what was the catch the last time
    // i just noticed that this if statement is not correct if(bounds)
    // it's always going to be executed our bounds currently are said to be an empty
    // object and an empty object is always truthy, so rather than checking for bounds
    // we can check for bounce.southwest and boundes.northeast if(bounds.sw && bounds.ne)
    // if bounds are an empty object then this here is going to be false 
    // let's test it out, as soon as i reload it everything works 
    // now let's go back to new york, we can see them on the map which works fine 
    // and hopefully they won't switch back this time, i dont see them switching back 
    // so this is possibly the solution to our problem 
    // let's try to visit one more place something like Paris to see if that workds
    // and unfortunately we still have restaurants from new york 
    // that means we hanen't properly reset the values of places when switching
    // to a new place, we can solve this problem really easily by simply removing
    // the coordinates from the dependency array           删除依赖里的 coordinates
    // we need to do that because now in the Header we're changing the coordinates 
    // and then they are being changed two times both in here on place changed 
    // and also on map right there, so we're going to remove them from the dependency here
    // and we only want to recall the get places data once we change the bounds 
    // if we now save this we can test it out and it should work
    // we are currently in London, let's try to switch to new york
    // let's see if we get new york restaurants we do as you can see they're all there
    // and let's try to switch to one more place something like Pairs
    // and we get all the restaurants in Pairs our explore new places search now completely 
    // works now we can get the restaurants for our current position we can move
    // around the map to get new restaurants each time and we can also use the search to get 
    // to any city or place on earth that was definitely a major feature 
    // entire travel advisor functionality is now done 
    // user can visit different places to get all the data about restaurants also hotels and 
    // finally attractions everything in one single place  
        useEffect(() => {
        if(bounds.sw && bounds.ne){
            setIsLoading(true)

            getPlacesData(type, bounds.sw, bounds.ne)
                .then((data) => {
                    setPlaces(data?.filter((place) => place.name && place.num_reviews > 0));
                    setFilteredPlaces([]);
                    setIsLoading(false);
                })
        }
    }, [type, bounds]);

```
