# General Assembly Project 02 : React Hackathon

Joshua King | Jeremy Giraudet

### Project Brief
The brief for this project was to create a basic web app using React and external APIs.

### Timeframe
48 hours

## Technologies used

* HTML5
* SASS/SCSS 
* JavaScript (ES6)
* React
* Webpack
* Bulma (CSS framework)
* Git/GitHub
* Express(added later for deployment purposes)


## Deployed web app

---> https://react-movie-library.herokuapp.com

## Code Installation

1. Clone or download the repo
2. Run ```yarn```  in Terminal
4. Run webpack dev server with ```yarn run serve```


## Overview
React Movie Library is a front-end web application which provides a movie recommendation based on movies that the user likes or enjoys already. 

![screenshot 2019-01-11 at 10 56 35](https://user-images.githubusercontent.com/43890120/54705434-b7782e00-4b34-11e9-90aa-ab735ef219e0.png)

## Process

### Planning
For this project we decided to use the OMDB API to provide us with the data needed to run our application. We also made plans to use the Twitter API, to provide recent tweets on the side of the page about the most recently viewed movies by the user.

### Separating the Workload
After some wireframing of what the application would look like, we decided to split our workload. I was tasked with working on the OMDB API. 

First, I worked on the search bar. We wanted a search bar that would actively search the OMDB API as the user typed, as we felt this provided a better experience for the user, in the case of a mistype or an inability to perhaps remember the full name of a film. This was accomplished using a getPossibleResults function, which also needed to filter through the results, as OMDB also provides TV shows and so we needed to narrow this information down. 

```
getPossibleResults(){
    axios
      .get(`${baseUrl}?s=${this.state.searchText}${apiKey}`)
      .then(res => {
        const filtered = res.data.Search.filter(result => result.Type==='movie')
        this.setState({ possibleResults: filtered })
      })
      .catch(err => console.log(err))
  }
 ```

### Adding Movies to the Library

Once the user sees a movie in the search results that he or she wishes to add to the library, the user can click on the movie to add it to his or her library. This was done by getting the id from e.target and then making an ensuing get request to the OMDB API for more information, and then adding it permanently down to the bottom of the page. It would also be added to the moviesWatched array in state of the main component.

```
getMovie(imdb, e){
    let imdbID
    if(e) imdbID = e.currentTarget.id
    if(imdb) imdbID = imdb
    this.setState({ searchText: '', possibleResults: []})
    axios
      .get(`${baseUrl}?i=${imdbID}${apiKey}`)
      .then(res => {
        this.setState({
          timeWatched: this.state.timeWatched + parseFloat(res.data.Runtime),
          moviesWatched: [res.data, ...this.state.moviesWatched]
        })
      })
    const movieName = e.currentTarget.firstElementChild.innerHTML.replace(' ', '')
    console.log(movieName)
    this.getRelatedMovies(imdbID)
  }
```

As can be seen at the bottom of this function, once the movie has been added to the library, a new function is run which searches for related movies.

### Getting Movie Recommendations

Upon reaching this point in the development process, we realized we had made a mistake in reading the documentation of the OMDB API. In the misunderstanding, we failed to realize that OMDB did not actually have an endpoint for getting related movies, based on another movies. We found another API which could, called TheMovieDB API. However, it required a bit of problem solving, as we did not wish to go back and rewrite all our old functions with the new API, due to the limited time remaining for our project.

So we found a way to link both of the APIs together in a way that, while not very efficient, did manage to connect and retreive the related movies. We needed to find a common connection between the two APIs and we did: both APIs use the IMDB IDs in their databases. So I was tasked with writing the code to connect the two. I used several functions to do this.

```
  getRelatedMovies(id){
    axios
      .get(`https://api.themoviedb.org/3/find/${id}?api_key=adfdea606b119c5d76189ff434738475&external_source=imdb_id`)
        .then(res => this.makeRecommendedRequest(res.data.movie_results[0].id))
  }
```
First I took the imdb from e.target(given from the first API) and made an axios request to the second API, to retreive the ID for their database, as searching the API by the IMDB ID will only return the ID for their database. Then I passed this ID into the next function


```
  makeRecommendedRequest(id){
    axios.get(`https://api.themoviedb.org/3/movie/${id}/recommendations?api_key=adfdea606b119c5d76189ff434738475`)
      .then(res => this.setState({ relatedMovies: res.data.results}))
  }
```
This function used the database ID to make a new request for the movie show route, where I could finally access the important data like title, image, etc. This data is then displayed on the side of the screen , with a click event on each recommendation, which would subsequently add it into the movie library as well, which is what the next function does.


```
getId(id){
    axios.get(`https://api.themoviedb.org/3/movie/${id}?api_key=adfdea606b119c5d76189ff434738475`)
      .then(res => this.getMovie(res.data.imdb_id))
  }
```

To add movies to the database, I then has to retreive the imdb ID again and pass it back through to the first(OMDB) API to properly save it to the library. 



## Conclusion and Thoughts for Future
Although we had many struggles during the day and a half we worked on this project, it was a very valuable learning experience in the end. It also forced us to come up with solutions on the fly (due to misunderstandings with the API), and was very helpful in teaching me how one can connect 2 or more APIs together in interesting ways. The fact that this was also a one page application made state management tricky at times. 

There are several things I would certainly change if I were to redo this project, and I just might. First being I would not use 2 different APIs of course, and I would simply use themoviedb.org API for the entire project. I would also take some time to work on the styling because I feel it could use a lot in that department.

For additional features, I would finish off the twitter API functionality, as we did not have enough time during the project time to finish this feature. I would also perhaps add users and a proper database to keep the user's library saved for future visits to the website. I would also like to rewrite the get related movies function to take into account ALL of the movies in the library, and not just the most recently added one. 







