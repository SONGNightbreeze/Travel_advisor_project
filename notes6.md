#### we can also see what's the weather like there where they're visiting 
#### Weather API
#### to find API we need for that   rapidapi.com/hub -> Search Weather Data
#### src/api/index.js
```js
import axios from 'axios'

export const getPlacesData = async (type, sw, ne) => {
    try {
        const {data: {data}} = await axios.get(URL, {
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
        return data;
    } catch(error){
        console.log(error)
    }
}

export const getWeatherData = async (lat, lng) => {
    try{
        // but what's actually api request we need to make 
        const {data} = await axios.get('https://community-open-weather-map.p.rapidapi.com/find',{
            // let's copy these options: params and headers, options object
            params:{
                lon:'0',
                lat: '0',
            },
            headers: {
                'x-rapidapi-key':'',
                'x-rapidapi-host':''
            }
        });        
    }catch(error) {
        console.log(error)
    }
}
```
#### lets make sure that we indeed pass that
#### so inside of App we're going to import get weather data right here
```js
import { getPlacesData, getWeatherData } from './api'


const App = () => {

    const [weatherData, setWeatherData] = useState([]);

    useEffect(() => {
        if (bounds.sw && bounds.ne){    
            setIsLoading(true)
            // and then just before calling the get places data, we're also going to call
            getWeatherData(coordinates.lat, coordinates.lng)
                .then((data) => setWeatherData(data)) 
                // create a setWeatherData state

            getPlacesData(type, bounds.sw, bounds.ne)
                .then((data) => {
                    setPlaces(data?.filter((place) => place.name && place.num_reviews > 0))
                    setFilteredPlaces([])
                    setIsLoading(false);
                })
        
        }
    }, [type, bounds]);
}
```
#### now that we know that we're passing the latitude and the longitude 
#### let's go back to api/index.js
```js
export const getWeatherData = async (lat, lng) => {
    try{
        const {data} = await axios.get('https://community-open-weather-map.p.rapidapi.com/find',{
            // let's set them 
            params:{lon:lng,lat: lat, },
            headers: {
                // finally the headers contain our api key
                // and rapid api host we can leave that for now 
                // later on we're going to switch that to environment variables
                'x-rapidapi-key':'',
                'x-rapidapi-host':''
            }
        });
        // and finally what do we do with the data, we're simply going to return it 
        return data;

    }catch(error) {
        console.log(error)
    }
}
```
#### this part is done, our data is here
#### in App is set to the state, 
```jsx
getWeatherData(coordinates.lat, coordinates.lng)
    .then((data) => setWeatherData(data)) 
```

#### and finally we want to pass it to the Map
#### App
```jsx
            <Grid item xs={12} md={8}>
                <Map
                    setCoordinates={setCoordinates}
                    setBounds={setBounds}
                    coordinates={coordinates}
                    places={filteredPlaces.length ? filteredPlaces : places}
                    setChildClicked={setChildClicked}
                    // just below im going to say whether data is equal to weather data
                    weatherData={weatherData}
                
                />
            </Grid>

```
#### we can go inside of Map 
#### we can get the weather data at the top from props
```jsx
const Map =({ setCoordinates, setBounds, coordinates, places, setChildClicked, weatherData})
    return (
        // ......

        // and just below the entire mapping of places 
        // we're going to open one more jsx block
                {
                    weatherData?.list?.map((data, i) => (
                        <div key={i} lat={data.coord.lat} lng={data.coord.lon}>
                            // inside of div is going to be our img
                            // in here we want to use the dynamic value that we're getting 
                            // from here 
                            // make icons a bit bigger give img a height 
                            // which is going to be equal to 100
                            <img height={100} src={`https://openweathermap.org/img/w/${data.weather[0].icon}.png`} />
                            // this entire string is also going to be in all the resources
                            // that you can find in the description 
                        </div>
                    ))}
            </GoogleMapReact>
        </div>
    )

```
#### display the icons and all of these cards

#### the last part i want to show you is how to change the styles of the map 
#### inside of options={}  in Map, we're going to have a object 
#### and that object have a few properties
```jsx
import mapStyles from './mapStyles'
    return (
        <div className={classes.mapContainer}>

            <GoogleMapReact
                bootstrapURLKeys={{key:''}}
                defaultCenter={coordinates}
                center = {coordinates}
                defaultZoom = {14}
                margin={[50,50,50,50]}
                // first one is going to be have disableDefaultUI
                options={{disableDefaultUI:true, 
                            zoomControl:true,
                            // these styles are not defined so we are yet to do that
                            // but i want to show you an incredibly cool way that you 
                            // can style the map extremely easily and immediately get 
                            // all of the styles for your map
                            // snazzymaps.com   in here you can search a lot of different
                            // maps that you can search for 可以选择一种类型
                            // the only thing you have to do to get these styles for your map
                            // is click expand code and then simply copy all the styles into 
                            // one file,
                            // i already found my prefered styles, 
                            // 在Map文件夹下创建文件 mapStyles.js
                            // 粘贴过来刚刚网站搜索到的内容到 mapStyles.js 然后再Map中引入
                            // the styles i've chosen just gave everything just a bit more
                            // color and remove the names of streets now we have only the 
                            // big city names and everything is looking just a bit cleaner
                            styles: mapStyles}}
                onChange={(e) => {
                    console.log(e) 
                    setCoordiantes({lat: e.center.lat, lng:e.center.lng })
                    setBounds({ne:e.center.lat, lng: e.center.lng})
                }}

```