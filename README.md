# Moh Movie App

---

## Project Summary

**Moh Movie App** is a modern, fully functional web application built using **React**, **Tailwind CSS**, and **Vite**. Its purpose is to provide users with a simple, yet powerful interface to:

- Explore popular movies
- Search for any movie by title
- View detailed information for each movie
- Maintain a personal list of favorite movies

The app uses **The Movie Database (TMDB) API**, which provides real-time movie data including titles, posters, ratings, release dates, and overviews. The project demonstrates key web development concepts including **React state management, API integration, conditional rendering, user interaction, and responsive design**.

The primary goal of this project is to provide a **practical learning experience** for working with APIs, React Hooks, dynamic rendering, and state management while producing a visually appealing, user-friendly application.

---

## Motivation & Idea

Movies are one of the most popular forms of entertainment worldwide. There are countless APIs and databases available, but creating an interactive **movie browsing application** is an excellent way to practice **front-end development** skills.  

The idea for this project came from:

- Wanting a **personalized movie browsing experience**
- Practicing **React Hooks and component design**
- Understanding **how to work with external APIs**
- Learning **state management and conditional rendering**
- Practicing **responsive layouts using Tailwind CSS**

By the end of this project, I aimed to have a **fully functional, user-friendly movie app** that can serve as a portfolio piece or as a foundation for a larger project, such as a full-fledged movie database with authentication and backend storage.

---

## Features

This app provides the following features:

1. **Search Movies**: Users can search for movies by typing in the movie name. Search queries dynamically fetch results from the TMDB API.
2. **Popular Movies**: By default, the app displays trending/popular movies.
3. **Favorites**: Users can add movies to their personal favorites list and remove them at any time.
4. **Movie Details**: Each movie card displays detailed information including **rating, release date, and overview**.
5. **Pagination**: Allows users to navigate through multiple pages of movie results easily.
6. **Loading State**: While fetching data, a loading spinner appears for better user experience.
7. **Error Handling**: If API calls fail, the app displays clear error messages.
8. **Responsive Design**: Fully responsive using Tailwind CSS, works well on mobile, tablet, and desktop.
9. **Interactive UI**: Hover effects, info toggling, and buttons improve usability and engagement.

---

## Tech Stack

This project uses the following technologies:

- **React**: Functional components and hooks for dynamic state management.
- **Vite**: Fast development environment and build tool.
- **JavaScript (ES6+)**: Core language for logic, event handling, and API integration.
- **Tailwind CSS**: Utility-first CSS framework for rapid, responsive design.
- **TMDB API**: Source of all movie data, including posters, ratings, and metadata.

---

# Code Explantion
---

Imports React & Hooks: useState and useEffect are used to manage component state and side effects.

**Imports Components**:

Spinner → shows a loading indicator while data is being fetched.

ErrorMessage → displays errors if something goes wrong.

SearchBar → input component for users to search for movies.

**State Variables**

movies – Stores the list of movies fetched from the API.

favMovie – Stores the user’s favorite movies.

movieSearch – Stores the current search query typed by the user.

page – Tracks the current page number for paginated API results.

totalPages – Tracks the total number of pages returned by the API.

loading – Boolean to indicate whether data is being loaded (used to show Spinner).

error – Stores error messages if an API request fails.

favMovieView – Determines whether the user is viewing search results or favorites.

infoMovieId – Stores the ID of a movie for which detailed info may be shown.

**API Key**

API_KEY → Retrieves your TMDB API key from environment variables, allowing the app to securely fetch movie data.

```javascript
import React, { useEffect, useState } from "react";
import Spinner from "./components/Spinner.jsx";
import ErrorMessage from "./components/ErrorMessage.jsx";
import SearchBar from "./components/SearchBar.jsx";

function App() {
  const [movies, setMovies] = useState([]);
  const [favMovie, setFavMovies] = useState([]);
  const [movieSearch, setMovieSearch] = useState("");
  const [page, setPage] = useState(1);
  const [totalPages, setTotalPages] = useState(1);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [favMovieView, setFavMovieView] = useState("search");
  const [infoMovieId, setInfoMovieId] = useState(null);

  const API_KEY = import.meta.env.VITE_TMDB_API_KEY;
}
```

This useEffect runs every time the user changes the search query, page number, or switches between viewing search results and favorites. Its job is to fetch movies from the TMDB API and update the app’s state.

**Check favorites view** – If the user is viewing their favorites list, the search results are cleared and no API call is made.

**Set up fetching** – When fetching starts, loading is set to true to show a spinner, and any previous errors are cleared.

**Build API URL** – If the user typed a search query, it calls the search endpoint; otherwise, it fetches popular movies. The query is encoded safely for URLs.

**Fetch data** – The app calls the API and waits for the response. If the request fails, an error is thrown.

**Update state** – On success, the movies are stored in movies, and totalPages is updated (max 500). On error, the error state is updated with a message.

**Finish fetching** – loading is set back to false, hiding the spinner.

In short, this useEffect handles fetching movies, managing loading and error states, and switching between search results and favorites in a clean, automatic way.

```javascript
useEffect(() => {
    if (favMovieView === "favorites") {
      setMovies([]);
      return;
    }

    const fetchMovies = async () => {
      setLoading(true);
      setError(null);
      try {
        let url;
        if (movieSearch) {
          url = `https://api.themoviedb.org/3/search/movie?api_key=${API_KEY.trim()}&query=${encodeURIComponent(
            movieSearch
          )}&page=${page}`;
        } else {
          url = `https://api.themoviedb.org/3/movie/popular?api_key=${API_KEY.trim()}&page=${page}`;
        }

        const res = await fetch(url);
        if (!res.ok) throw new Error("Failed to fetch movies");

        const data = await res.json();
        setMovies(data.results);
        setTotalPages(Math.min(data.total_pages || 1, 500));
      } catch (err) {
        setError(err.message || "Failed to fetch movies");
      } finally {
        setLoading(false);
      }
    };

```

This code handles the **core functionality of the app**: searching for movies, managing favorites, toggling movie details, and displaying movie cards.

When the search term, page number, or view mode changes, fetchMovies() is called to load the relevant movies from the API.

handleSearch updates the search term and resets the page to 1, so searches always start from the first page.

addFavorite adds a movie to the favorites list if it isn’t already there, while removeFavorite removes it. isFavorite checks whether a movie is in the favorites list.

toggleInfo shows or hides detailed information for a movie when clicked. Clicking the same movie again hides the details.

renderMovieCard creates a visual card for each movie, showing the poster if available, or a placeholder if not. Clicking the poster or placeholder opens the movie’s page on TMDB. Each card also has styling for hover effects, shadows, and rounded corners, making the UI interactive and visually appealing.

In short, this section **connects user actions with the app state and UI**, letting users search movies, view details, and manage favorites smoothly.

```javascript

    fetchMovies();
  }, [movieSearch, page, favMovieView]);

  const handleSearch = (term) => {
    setMovieSearch(term);
    setPage(1);
  };

  const addFavorite = (movie) => {
    if (!favMovie.find((m) => m.id === movie.id)) {
      setFavMovies([...favMovie, movie]);
    }
  };

  const removeFavorite = (movie) => {
    setFavMovies(favMovie.filter((m) => m.id !== movie.id));
  };

  const isFavorite = (movie) => favMovie.some((m) => m.id === movie.id);

  const toggleInfo = (movieId) => {
    setInfoMovieId(infoMovieId === movieId ? null : movieId);
  };

  const renderMovieCard = (movie) => {
    const tmdbLink = `https://www.themoviedb.org/movie/${movie.id}`;

    return (
      <div
        key={movie.id}
        className="relative bg-gray-100 rounded-xl shadow-lg hover:shadow-yellow-500/50 transition-all duration-300 flex flex-col items-center cursor-pointer overflow-hidden group"
      >
        {/* Poster */}
        {movie.poster_path ? (
          <img
            src={`https://image.tmdb.org/t/p/w500${movie.poster_path}`}
            alt={movie.title}
            className="rounded-lg mb-2 shadow-md"
            onClick={() => window.open(tmdbLink, "_blank")}
          />
        ) : (
          <div
            className="h-64 bg-gray-300 flex items-center justify-center mb-2 rounded-lg"
            onClick={() => window.open(tmdbLink, "_blank")}
          >
            No Image
          </div>
        )}

```

This code handles **interactive overlays and favorite buttons on each movie card**:

**Movie title on hover** – A semi-transparent black overlay appears in the center of the card when the user hovers over it, showing the movie’s title. The overlay uses smooth fade-in/fade-out animations (opacity-0 → opacity-100) and does not block clicks (pointer-events-none).

**Favorite buttons** – Below the poster, a button lets the user **add or remove the movie from favorites**:

If the movie is already a favorite (isFavorite(movie) is true), a red “Remove from Favorites” button appears. Clicking it calls removeFavorite(movie).

If the movie is not a favorite, a green “**Add to Favorites**” button appears. Clicking it calls addFavorite(movie).

e.stopPropagation() prevents the click from triggering other card actions, like opening the TMDB link.

The buttons have **hover effects, rounded corners, and full-width styling**, making the UI intuitive and visually appealing.

In short, this section **enhances the UI with hover effects and interactive buttons**, allowing users to see movie titles and manage their favorites directly from the movie cards.

```javascript

 {/* Movie title on hover */} <div className="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 bg-black/60 text-white font-bold text-center text-lg py-1 px-2 opacity-0 group-hover:opacity-100 transition-opacity pointer-events-none"> {movie.title} </div> <div className="flex flex-col items-center mt-2 px-2 w-full"> {isFavorite(movie) ? ( <button className="px-3 py-1 bg-red-600 text-white rounded mb-2 hover:bg-red-700 transition w-full" onClick={(e) => { e.stopPropagation(); removeFavorite(movie); }} > Remove from Favorites </button> ) : ( <button className="px-3 py-1 bg-green-600 text-white rounded mb-2 hover:bg-green-700 transition w-full" onClick={(e) => { e.stopPropagation(); addFavorite(movie); }} > Add to Favorites </button> )}

```

This section of the code handles **movie details, pagination, tabs, search, and rendering the main UI**:

**Show Info Button** – Each movie card has a yellow “Show Info” button. Clicking it calls toggleInfo(movie.id), which either shows or hides detailed information about the movie, such as **rating, release date, and overview**. This info appears in a small styled box below the button when infoMovieId matches the movie’s ID.

**Pagination** – Functions handlePrevPage, handleNextPage, and handlePageClick let users navigate through multiple pages of movie results.

renderPagination dynamically generates page buttons, highlighting the current page.

“Prev” and “Next” buttons are disabled at the first or last page.

This makes browsing through large sets of movies smooth and user-friendly.

**App Header** – The top of the page displays the app title (“Moh Movie App”) with bold styling and a drop shadow.

**Tabs for Views** – Two tabs let the user switch between:

**Search / Popular** – Shows search results or popular movies.

**Favorites** – Shows the user’s favorite movies.
The active tab is highlighted with a yellow bottom border and bold text.

**Search Bar **– Displayed only in the “Search / Popular” tab, it calls handleSearch when the user types a query, updating the search results.

**Loading & Error States** – While fetching data, a Spinner component is shown. If an error occurs, ErrorMessage displays a message.

**Movie Grid**–

In the search view, movies are displayed in a **responsive grid**.

If no movies match the search, a friendly “Can’t find any movies” message appears.

In the favorites view, the grid shows all favorited movies or a message if the list is empty.

**Overall Layout & Styling** –

Uses Tailwind CSS for spacing, grids, colors, shadows, and responsive design.

Ensures the UI is clean, interactive, and mobile-friendly.


```javascript

 <button
            className="px-3 py-1 bg-yellow-400 text-black rounded mb-2 hover:bg-yellow-500 transition w-full"
            onClick={(e) => {
              e.stopPropagation();
              toggleInfo(movie.id);
            }}
          >
            Show Info
          </button>

          {infoMovieId === movie.id && (
            <div className="bg-gray-200 p-2 rounded w-full text-left text-sm text-gray-800 mt-2 shadow-inner">
              <p>
                <strong>Rating:</strong> {movie.vote_average || "N/A"}
              </p>
              <p>
                <strong>Release Date:</strong> {movie.release_date || "N/A"}
              </p>
              <p>
                <strong>Overview:</strong>{" "}
                {movie.overview || "No description available."}
              </p>
            </div>
          )}
        </div>
      </div>
    );
  };

  // Pagination logic
  const handlePrevPage = () => {
    if (page > 1) setPage(page - 1);
  };

  const handleNextPage = () => {
    if (page < totalPages) setPage(page + 1);
  };

  const handlePageClick = (num) => setPage(num);

  const renderPagination = () => {
    const pages = [];
    const maxButtons = 5;
    let startPage = Math.max(1, page - Math.floor(maxButtons / 2));
    let endPage = Math.min(totalPages, startPage + maxButtons - 1);
    startPage = Math.max(1, endPage - maxButtons + 1);

    for (let i = startPage; i <= endPage; i++) {
      pages.push(
        <button
          key={i}
          className={`px-3 py-1 rounded ${
            i === page
              ? "bg-yellow-500 text-black font-bold"
              : "bg-gray-400 text-gray-900 hover:bg-yellow-400"
          }`}
          onClick={() => handlePageClick(i)}
        >
          {i}
        </button>
      );
    }
    return (
      <div className="flex gap-2 justify-center mt-6">
        <button
          className="px-3 py-1 rounded bg-gray-400 text-gray-900 hover:bg-yellow-400"
          onClick={handlePrevPage}
          disabled={page === 1}
        >
          Prev
        </button>
        {pages}
        <button
          className="px-3 py-1 rounded bg-gray-400 text-gray-900 hover:bg-yellow-400"
          onClick={handleNextPage}
          disabled={page === totalPages}
        >
          Next
        </button>
      </div>
    );
  };

  return (
    <div className="min-h-screen bg-gray-100 py-6 flex flex-col items-center">
      <h1 className="text-5xl font-extrabold mb-6 drop-shadow-2xl text-gray-900">
        Moh Movie App
      </h1>

      {/* Tabs */}
      <div className="flex gap-4 mb-6 border-b-2 border-gray-400">
        <button
          className={`text-lg px-4 py-2 ${
            favMovieView === "search"
              ? "border-b-4 border-yellow-500 font-bold text-yellow-500"
              : "text-gray-600"
          }`}
          onClick={() => setFavMovieView("search")}
        >
          Search / Popular
        </button>

        <button
          className={`text-lg px-4 py-2 ${
            favMovieView === "favorites"
              ? "border-b-4 border-yellow-500 font-bold text-yellow-500"
              : "text-gray-600"
          }`}
          onClick={() => setFavMovieView("favorites")}
        >
          Favorites
        </button>
      </div>

      {/* Search */}
      {favMovieView === "search" && (
        <div className="w-full max-w-md mb-6">
          <SearchBar
            onSearch={handleSearch}
            className="bg-white text-gray-900 font-bold"
          />
        </div>
      )}

      {/* Loading & Error */}
      {loading && <Spinner />}
      {error && <ErrorMessage message={error} />}

      {/* Movies */}
      {favMovieView === "search" && !loading && !error && (
        <>
          {movies.length > 0 ? (
            <>
              <div className="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-6 w-full max-w-5xl">
                {movies.map(renderMovieCard)}
              </div>
              {renderPagination()}
            </>
          ) : (
            <p className="text-gray-500 text-lg mt-4">
              Can't find any movies matching your search.
            </p>
          )}
        </>
      )}

      {/* Favorites */}
      {favMovieView === "favorites" && (
        <div className="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-6 w-full max-w-5xl">
          {favMovie.length > 0
            ? favMovie.map(renderMovieCard)
            : (
              <p className="text-gray-500 text-lg mt-4">No favorite movies added.</p>
            )}
        </div>
      )}
    </div>
  );
}

```








## Project Structure

```text
movie-app/
│
├── src/
│   ├── components/
│   │   ├── ErrorMessage.jsx     # Displays error messages for API or network errors
│   │   ├── SearchBar.jsx        # Controlled input component for movie search
│   │   └── Spinner.jsx          # Loading spinner component
│   │
│   ├── App.jsx                  # Main component, handles state, API, rendering
│   └── main.jsx                 # Entry point, renders App
│
├── public/
│   └── index.html               # Main HTML file
├── .env                         # Environment variables including TMDB API key
├── package.json                 # Project dependencies
└── README.md                    # Project documentation



---
