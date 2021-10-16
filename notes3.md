### Place Card

> we already have the name for all of restaruants, but these restaurant have so much information
> so let's render a nice looking card for each one of them 
> as you can see, we've already seen this we have the address object, awards,
> booking, cuisine, and make the card look better include more information if you want 
> you can include the distance the establishment type and everything else you'd want 
> but this case we're gonna make a decent looking card 
> so let's import something in material ui

#### PlaceDetails.jsx
```jsx
import React from 'react'
import { Box, Typography, Button, Card, CardMedia, CardContent, CardActions, Chip} from '@material-ui/core'
// Typography for text,CardMedia for image, CardActions that's where our buttons are going to be

import LocationOnIcon from '@material-ui/icons/LocationOn' 
import PhoneIcon from '@material-ui/icons/Phone'

import Rating from '@material-ui/lab/rating'


import useStyles from './styles'

const PlaceDetails = ({place}) => {
    const classes = useStyles();

    return (
        // we can wrap everything inside of a card component
        // elevation={6} will give a nice shadow effect 
        <Card elevation={6}>
            <CardMedia
                style={{height: 350}}
                // then we're also gonna use the actual image 
                // the image we have a use a ternary expression to check if the restaurant
                // actually has the image
                image={place.photo? place.photo.images.large.url: ...}
                // place.photo.images.large.url this is going to be where the url of the image
                // will be stored at
                // and finally if we dont have image , we can use a demo image of a restaruant
                // that i found online, you will find this link somewhere all the other styles 
                
                title={place.name}
            />
            // below that, we're going to have a card content,
            // everything else is going to go inside of the CardContent
            <CardContent>
                <Typography gutterBottom>{place.name}</Typography>
                //gutterBottom property means we're just going to give it some extra margin
                //at the bottom and variant is going to be quite big, it's going to be heading 5
                //inside of there, we simply want to render the dynamic value of name that place

                // it might be better if we put our browser side by side with the editor
                // that way we'll be able to see the changes live as we make them
                
                // our rating should have been just below this typography to display 
                <Box display='flex' justifyContent='space-between'>
                    <Rating value={Number(place.rating)} readOnly  /> 
                    <Typography gutterBottom variant='subtitle1'>{place.num_reviews} reviews</Typography>
                </Box>
                
                // we're gonna to have a box component
                <Box display='flex' justifyContent='space-between'>
                    <Typography variant='subtitle1'>Price</Typography>
                    <Typography gutterBottom variant='subtitle1'>{place.price_level}</Typography>
                </Box>
                // we can copy this exact part and we're gonna use it for the ranking
                <Box display='flex' justifyContent='space-between'>
                    <Typography variant='subtitle1'>Ranking</Typography>
                    <Typography gutterBottom variant='subtitle1'>{place.ranking}</Typography>
                </Box>

                // we want to map over all of the awards that a restaurant has gathered
                // we can do that by opening a dynamic block
                // in the map accepts a callback function, we're not going to have curly braces
                // we're simply going to have parenthesses because we want to instantly 
                // return something 
                {place?.awards?.map(() => (
                    // in side of the map, we get a singular value of what we're mapping through
                    // so in this case we're getting an award for each award we want to 
                    // show a box 
                    // m stands for margin, y stands for axis top and bottom
                    // so this is going to give it a margin of top and bottom one 
                    // as you can see it's going to space them out properly 
                    // this is material-ui feture 
                    <Box  my={1} display='flex' justifyContent='space-between' alignItems='center'>
                        // inside of there we can render a small image 
                        <img src={award.images.small} alt={award.display_name} />               
                        // below our image we want to have a typography explaining that
                        // specific award 
                        <Typography variant='subtitle2' color='textSecondary'>{award.display_name}</Typography>
                        // we can see this restaurant get 3 certificate of excellence
                    </Box>
                ))}
                // we want to know which type of food does each restaurant prepare
                // but we're only going to use the name of the food, so we can simply say
                {place?.cuisine?.map(({ name }) => (
                    // for each one, we want to show a chip component
                    <Chip key={name} size='small' label={name} className={classes.chip} />
                ))}
                // TypeError: cannot read property 'map' of undefined
                // so one of the many restaurant didn't have a cuisine property 
                // therefore we weren't able to map over  place?.cuisine?.map()
            </CardContent>   
        </Card>
    )
}

```
#### TypeError: Cannot read property 'sw' of null 
#### look like we also have one issue inside of our App,
#### we're trying to send the bounce that southwest and northeast 
#### this worked before because our bounds were already filled 
#### but now as we dont have them it's not possible to call the .sw of null
#### so inside of here line 13 make sure to an empty object as well
### App.js
```js 
const [bounds, setBounds] = useState({});
```

### PlaceDetails.jsx
```jsx
// we have to know the address of the restaurants
// if place question dot mark that address if it exist in that case we want to render a jsx
// block that' going to show the address, that is going to be a typography component
{place?.address && (
    <Typography gutterBottom variant='subtitle2' color='textSecondary' className={classes.subtitle}>
        // we're going to use our LocationOnIcon it is a self-closing tag
        <LocationOnIcon /> {place.address} 
    </Typography>
)}
// finally we also want to know phone number here copy this whole part 
{place?.phone && (
    <Typography gutterBottom variant='subtitle2' color='textSecondary' className={classes.spacing}>
        <PhoneIcon /> {place.phone} 
    </Typography>
)}
// the final part is adding the two buttons that can lead us to the trip advisor page
// for a restaurant and to the restaurant's own website 
<CardActions>
    // onClick we want to create a callback function and then simply call the window 
    // window.open() and in here we have to use the url we want to open 
    // window.open(place.web_url) the window.open also accepts a second parameter
    // which is '_blank' if we add this it's going to open that website in a new tab 
    // opposed to just replace our own website
    <Button size='small' color='primary' onClick={() => window.open(place.web_url, '_blank')}>
        Trip Advisor
    </Button>
    <Button size='small' color='primary' onClick={() => window.open(place.web_website, '_blank')}>
        Website
    </Button>
</CardActions>
```
--------------------------------------------------------------------------------------------
### next really important part is taking these places from the app and then displaying
### them on the Map, we can do that by going to App.js
```js
return (
    <>
        <CssBaseline />
        <Header />
        <Grid container spacing={3} style= {{width:'100%'}}>
            <Grid item xs={12} md={4}>
                <List places={places} />
            </Grid>
            <Grid item xs={12} md={8}>
                <Map
                    setCoordinates={setCoordinates}
                    setBounds={setBounds}
                    coordinates={coordinates}
                    // taking the same places we've been passing over to our list
                    // and then sending them over to our map as well 
                    places={places}
                    // as you can see, we were sending a lot of props from one component to 
                    // another, this is still okey, because we're only sending it one level deep
                    // from App to Map, bu we're not sending it two or three levels deep
                    // whenever that happens for you i would advise you to switch to using 
                    // either redux or more simply react context,                 
                />
            </Grid>
        </Grid>
    </>
)
```
### we're passing the places so now inside of Map
```js
// we can finally destructure them from the props 
const Map = ({ setCoordinates, setBounds, coordinates, places}) => {
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
                onChange={(e) => {
                    console.log(e) 
                    setCoordiantes({lat: e.center.lat, lng:e.center.lng })
                    setBounds({ne:e.center.lat, lng: e.center.lng})
                }}
                onChildClick={}
            >
            // how do we actually show the pins on the map
            // you simply have to render them right here inside of map
                {places?.map((place,i) => (
                    // for each place we want to render a div
                    <div
                        className={classes.markerContainer}
                        lat={Number(place.latitude)}
                        lng={Number(place.longitude)}
                        // where we're getting these values as strings 
                        // in here they have to be numbers so we can use the number constructor
                        // to convert values into numbers so Number(place.latitude)
                    
                        // now since we're mapping over these div, we have to have the key
                        key ={i}
                    >
                        // in side of <div> we want to render different things depending on 
                        // if we're on mobile device or on desktop
                        {
                            //if we are on mobile device so if is mobile in that case
                            // we simply want to render out the icon
                            isMobile ? (
                                <LocationOnOutlinedIcon color='primary' fontSize='large' />
                            ) : (
                                // now if we're not on mobile we render big more information
                                <Paper elevation={3} className={classes.paper}> 
                                    <Typography className={classes.typography} variant='subtitle2' gutterBottom>
                                        {place.name}
                                    </Typography>
                                    <img 
                                        className={classes.pointer}
                                        // we also want the source 
                                        // we can copy the source from placeDetails at the top
                                        // because it's going to be the same
                                        src ={place.photo ? place.photo.images.large.url : ''}
                                        alt ={place.name}
                                    />
            // as you can see right now we have just the pins even though we are on desktop
            // based on what we've seen that means that this isMobile variable is actually
            // the isDesktop and that is mobile so if the min width is larger than 600px
            // that means that it is desktop,  

                                    // the last thing is going to be the rating 
                                    // add the rating we'll use that experimental 
                                    // rating component it is a self-closing tag
                                    // and we're going to give it a property size
                                    // which is equal to small we're also going to give it
                                    // a value which is going to be equal to a number 
                                    // we need to use that number constructor to get the rating
                                    // and then we're gonna pass the place.rating
                                    // finally we need to pass the readOnly property because
                                    // our users cannot change the ratings 
                                    <Rating size='small' value={Number(place.rating)} readOnly  /> 

                                </Paper>
                                //<Paper> is basically a div with a background and it's going to
                                //have elevation equal to 3 will give it some box shadow
                                
                                // we should be seeing all of our <paper> restaurants on the map
                                // and now the left side we should also see the rating 
                                // in our list
                            )
                        }
                    </div>
                ))}
            </GoogleMapReact>
        </div>
    );

}

```

### once we got all of the data from the rapidapi we just simply had to display it on the left side and on the map
### the key thing here was knowing that we have to use the latitude and longitude to display those places right inside of the map
### now that we're displaying these places on the map let's make them clickable