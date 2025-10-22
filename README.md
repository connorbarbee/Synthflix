# Synthflix
# A one-file app that allows you to upload and watch media.

import React, { useState, useMemo, useEffect } from 'react';

// --- Icon Components (using inline SVGs for portability) ---

const Clapperboard = (props) => (
  <svg
    xmlns="http://www.w3.org/2000/svg"
    width="24"
    height="24"
    viewBox="0 0 24 24"
    fill="none"
    stroke="currentColor"
    strokeWidth="2"
    strokeLinecap="round"
    strokeLinejoin="round"
    {...props}
  >
    <path d="M20.2 6 3 11l-.9-2.4c-.3-1.1.1-2.2 1-2.8l12.7-6.9c.7-.4 1.6-.3 2.2.3Z" />
    <path d="m7 12.2 13-7" />
    <path d="M11.8 17.8 3 22l-.9-2.4c-.3-1.1.1-2.2 1-2.8l8.7-4.7" />
    <path d="m15 16.2 7.8-4.2" />
  </svg>
);

const Home = (props) => (
  <svg
    xmlns="http://www.w3.org/2000/svg"
    width="24"
    height="24"
    viewBox="0 0 24 24"
    fill="none"
    stroke="currentColor"
    strokeWidth="2"
    strokeLinecap="round"
    strokeLinejoin="round"
    {...props}
  >
    <path d="m3 9 9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z" />
    <polyline points="9 22 9 12 15 12 15 22" />
  </svg>
);

const UploadCloud = (props) => (
  <svg
    xmlns="http://www.w3.org/2000/svg"
    width="24"
    height="24"
    viewBox="0 0 24 24"
    fill="none"
    stroke="currentColor"
    strokeWidth="2"
    strokeLinecap="round"
    strokeLinejoin="round"
    {...props}
  >
    <path d="M4 14.899A7 7 0 1 1 15.71 8h1.79a4.5 4.5 0 0 1 2.5 8.242" />
    <path d="M12 12v9" />
    <path d="m16 16-4-4-4 4" />
  </svg>
);

const AlertTriangle = (props) => (
  <svg
    xmlns="http://www.w3.org/2000/svg"
    width="24"
    height="24"
    viewBox="0 0 24 24"
    fill="none"
    stroke="currentColor"
    strokeWidth="2"
    strokeLinecap="round"
    strokeLinejoin="round"
    {...props}
  >
    <path d="m21.73 18-8-14a2 2 0 0 0-3.46 0l-8 14A2 2 0 0 0 4 21h16a2 2 0 0 0 1.73-3Z" />
    <path d="M12 9v4" />
    <path d="M12 17h.01" />
  </svg>
);

const X = (props) => (
  <svg
    xmlns="http://www.w3.org/2000/svg"
    width="24"
    height="24"
    viewBox="0 0 24 24"
    fill="none"
    stroke="currentColor"
    strokeWidth="2"
    strokeLinecap="round"
    strokeLinejoin="round"
    {...props}
  >
    <path d="M18 6 6 18" />
    <path d="m6 6 12 12" />
  </svg>
);


// --- Initial Data ---
// All movies are now in one list, sorted alphabetically
const initialMovies = [
  { id: 1, title: 'Cybernetic Dawn', description: 'In a future ruled by machines...', poster: 'https://placehold.co/300x450/333333/FFFFFF?text=Cybernetic%20Dawn' },
  { id: 101, title: 'Synthwave Runner', poster: 'https://placehold.co/300x450/333333/FFFFFF?text=Synthwave%20Runner' },
  { id: 102, title: 'Neon Nights', poster: 'https://placehold.co/300x450/333333/FFFFFF?text=Neon%20Nights' },
  { id: 103, title: 'Digital Ghost', poster: 'https://placehold.co/300x450/333333/FFFFFF?text=Digital%20Ghost' },
  { id: 104, title: 'Grid Warriors', poster: 'https://placehold.co/300x450/333333/FFFFFF?text=Grid%20Warriors' },
  { id: 105, title: 'The Last Datastream', poster: 'https://placehold.co/300x450/333333/FFFFFF?text=The%20Last%20Datastream' },
  { id: 201, title: 'Galaxy\'s Edge', poster: 'https://placehold.co/300x450/333333/FFFFFF?text=Galaxy\'s%20Edge' },
  { id: 202, title: 'Quantum Leap', poster: 'https://placehold.co/300x450/333333/FFFFFF?text=Quantum%20Leap' },
  { id: 203, title: 'Project: Chimera', poster: 'https://placehold.co/300x450/333333/FFFFFF?text=Project:%20Chimera' },
  { id: 204, title: 'Time Shift', poster: 'https://placehold.co/300x450/333333/FFFFFF?text=Time%20Shift' },
  { id: 301, title: 'Neo-Tokyo 2049', poster: 'https://placehold.co/300x450/333333/FFFFFF?text=Neo-Tokyo%202049' },
  { id: 302, title: 'Chrome Syndicate', poster: 'https://placehold.co/300x450/333333/FFFFFF?text=Chrome%20Syndicate' },
  { id: 303, title: 'Interface', poster: 'https://placehold.co/300x450/333333/FFFFFF?text=Interface' },
  { id: 304, title: 'Sub-Zero Protocol', poster: 'https://placehold.co/300x450/333333/FFFFFF?text=Sub-Zero%20Protocol' },
  { id: 305, title: 'Mainframe', poster: 'https://placehold.co/300x450/333333/FFFFFF?text=Mainframe' },
].sort((a, b) => a.title.localeCompare(b.title));


// --- React Components ---

/**
 * Header Component
 * Displays the logo and navigation
 */
function Header({ onNavigate, activePage }) {
  const NavLink = ({ page, icon, children }) => (
    <button
      onClick={() => onNavigate(page)}
      className={`flex items-center space-x-2 text-sm font-medium rounded-md px-3 py-2 transition-colors ${
        activePage === page
          ? 'text-white bg-white/10'
          : 'text-gray-400 hover:text-white hover:bg-white/5'
      }`}
    >
      {icon}
      <span>{children}</span>
    </button>
  );

  return (
    <header className="fixed top-0 left-0 right-0 z-20 p-4 bg-gradient-to-b from-black/80 to-transparent flex items-center justify-between">
      <div className="flex items-center space-x-8">
        <h1 className="text-2xl font-bold text-white flex items-center space-x-2">
          <Clapperboard className="w-7 h-7" />
          <span>Synthflix</span>
        </h1>
        <nav className="flex items-center space-x-4">
          <NavLink page="browse" icon={<Home className="w-5 h-5" />}>
            Browse
          </NavLink>
          <NavLink page="upload" icon={<UploadCloud className="w-5 h-5" />}>
            Upload
          </NavLink>
        </nav>
      </div>
    </header>
  );
}

/**
 * MovieCard Component
 * Displays a single movie poster, clickable to edit
 */
function MovieCard({ movie, onSelectMovie }) {
  return (
    <button
      onClick={() => onSelectMovie(movie.id)}
      className="flex-shrink-0 w-40 md:w-48 group relative rounded-lg overflow-hidden shadow-lg transform transition-all duration-300 hover:scale-105 hover:z-10 focus:outline-none focus:ring-2 focus:ring-white focus:ring-offset-2 focus:ring-offset-black"
    >
      <img
        src={movie.poster}
        alt={movie.title}
        className="w-full h-full object-cover"
        onError={(e) => { e.target.onerror = null; e.target.src = 'https://placehold.co/300x450/333/FFF?text=Error'; }}
      />
      <div className="absolute inset-0 bg-black/60 opacity-0 group-hover:opacity-100 transition-opacity duration-300 flex items-center justify-center p-2">
        <h3 className="text-white text-center font-semibold">{movie.title}</h3>
      </div>
    </button>
  );
}

/**
 * CategoryRow Component
 * Displays a horizontally scrolling row of movies
 */
function CategoryRow({ title, movies, onSelectMovie }) {
  return (
    <div className="mb-8">
      <h2 className="text-xl md:text-2xl font-semibold text-white mb-4 px-6 md:px-10">
        {title}
      </h2>
      <div className="relative">
        <div className="flex space-x-4 overflow-x-auto overflow-y-hidden pb-4 px-6 md:px-10 scrollbar-thin scrollbar-thumb-gray-700 scrollbar-track-transparent">
          {movies.map((movie) => (
            <MovieCard key={movie.id} movie={movie} onSelectMovie={onSelectMovie} />
          ))}
        </div>
      </div>
    </div>
  );
}

/**
 * BrowsePage Component
 * Displays the main browsing interface
 */
function BrowsePage({ movies, onSelectMovie }) {
  return (
    <div className="pt-24">
      <CategoryRow
        title="All Media (A-Z)"
        movies={movies}
        onSelectMovie={onSelectMovie}
      />
    </div>
  );
}

/**
 * UploadPage Component
 * Displays the functional upload form
 */
function UploadPage({ onAddMovie, onNavigate }) {
  const [description, setDescription] = useState('');
  const [file, setFile] = useState(null);
  const [fileName, setFileName] = useState('No file selected');

  const handleFileChange = (e) => {
    const selectedFile = e.target.files[0];
    if (selectedFile) {
      setFile(selectedFile);
      setFileName(selectedFile.name);
    }
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    if (!file) {
      // In a real app, show a proper error message
      console.error("No file selected");
      return;
    }

    // Get title from file name, removing extension
    const newTitle = file.name.replace(/\.[^/.]+$/, "");
    
    // Create a single "SYN" placeholder poster
    const newPoster = `https://placehold.co/300x450/333333/FFFFFF?text=SYN`;

    const newMovie = {
      id: Date.now(), // Use timestamp for a unique ID
      title: newTitle,
      description: description,
      poster: newPoster,
    };

    onAddMovie(newMovie); // Add the movie to the main state
    onNavigate('browse'); // Go back to the browse page
  };

  return (
    <div className="pt-24 px-6 md:px-10 text-white min-h-screen">
      <div className="max-w-3xl mx-auto">
        <h1 className="text-3xl font-bold mb-6">Upload Your Video</h1>

        {/* Technical Limitation Disclaimer */}

        {/* Upload Form UI */}
        <div className="bg-zinc-800 p-6 rounded-lg shadow-xl">
          <form onSubmit={handleSubmit}>
            <div className="mb-4">
              <label htmlFor="description" className="block text-sm font-medium text-gray-300 mb-2">
                Description
              </label>
              <textarea
                id="description"
                rows="4"
                placeholder="A short summary of your video..."
                className="w-full px-3 py-2 bg-zinc-700 border border-zinc-600 rounded-md focus:outline-none focus:ring-2 focus:ring-white"
                value={description}
                onChange={(e) => setDescription(e.target.value)}
              ></textarea>
            </div>

            <div className="mb-6">
              <label htmlFor="file" className="block text-sm font-medium text-gray-300 mb-2">
                .mp4 File
              </label>
              <div className="mt-1 flex justify-center px-6 pt-5 pb-6 border-2 border-zinc-600 border-dashed rounded-md">
                <div className="space-y-1 text-center">
                  <UploadCloud className="mx-auto h-12 w-12 text-gray-400" />
                  <div className="flex text-sm text-gray-400">
                    <label
                      htmlFor="file-upload"
                      className="relative cursor-pointer bg-zinc-700 rounded-md font-medium text-gray-300 hover:text-white focus-within:outline-none focus-within:ring-2 focus-within:ring-offset-2 focus-within:ring-offset-zinc-800 focus-within:ring-white px-1"
                    >
                      <span>Upload a file</span>
                      <input id="file-upload" name="file-upload" type="file" className="sr-only" accept=".mp4,.mov" onChange={handleFileChange} />
                    </label>
                    <p className="pl-1">or drag and drop</p>
                  </div>
                  <p className="text-xs text-gray-500">{fileName}</p>
                </div>
              </div>
            </div>

            <button
              type="submit"
              className="w-full py-3 px-4 bg-gray-700 text-white font-bold rounded-md transition-colors hover:bg-gray-600 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-offset-zinc-800 focus:ring-white"
            >
              Upload Video
            </button>
          </form>
        </div>
      </div>
    </div>
  );
}

/**
 * EditModal Component
 * A modal to edit the movie poster URL
 */
function EditModal({ movie, onClose, onSave }) {
  const [posterUrl, setPosterUrl] = useState(movie.poster);

  if (!movie) return null;

  const handleSubmit = (e) => {
    e.preventDefault();
    onSave(movie.id, posterUrl);
    onClose();
  };

  return (
    <div className="fixed inset-0 z-30 flex items-center justify-center bg-black/80 backdrop-blur-sm">
      <div className="bg-zinc-800 text-white rounded-lg shadow-2xl w-full max-w-md m-4">
        <div className="flex items-center justify-between p-4 border-b border-zinc-700">
          <h2 className="text-xl font-semibold">Edit Poster</h2>
          <button onClick={onClose} className="text-gray-400 hover:text-white">
            <X className="w-6 h-6" />
          </button>
        </div>
        <form onSubmit={handleSubmit}>
          <div className="p-6">
            <h3 className="text-lg font-medium mb-2">{movie.title}</h3>
            <img
              src={posterUrl}
              alt="Poster preview"
              className="w-32 h-auto rounded-md mb-4 bg-zinc-700"
              onError={(e) => { e.target.onerror = null; e.target.src = 'https://placehold.co/300x450/333/FFF?text=Error'; }}
            />
            <label htmlFor="posterUrl" className="block text-sm font-medium text-gray-300 mb-2">
              New Poster URL
            </label>
            <input
              type="text"
              id="posterUrl"
              className="w-full px-3 py-2 bg-zinc-700 border border-zinc-600 rounded-md focus:outline-none focus:ring-2 focus:ring-white"
              value={posterUrl}
              onChange={(e) => setPosterUrl(e.g.target.value)}
              placeholder="https://.../image.png"
            />
          </div>
          <div className="p-4 bg-zinc-900/50 rounded-b-lg flex justify-end space-x-3">
            <button
              type="button"
              onClick={onClose}
              className="px-4 py-2 bg-gray-600 text-white font-medium rounded-md transition-colors hover:bg-gray-500 focus:outline-none focus:ring-2 focus:ring-white"
            >
              Cancel
            </button>
            <button
              type="submit"
              className="px-4 py-2 bg-gray-700 text-white font-bold rounded-md transition-colors hover:bg-gray-600 focus:outline-none focus:ring-2 focus:ring-white"
            >
              Save Changes
            </button>
          </div>
        </form>
      </div>
    </div>
  );
}

/**
 * Main App Component
 */
export default function App() {
  const [activePage, setActivePage] = useState('browse'); // 'browse' or 'upload'
  
  // Load movies from local storage on initial render
  const [movies, setMovies] = useState(() => {
    const savedMovies = localStorage.getItem('synthflixMovies');
    if (savedMovies) {
      return JSON.parse(savedMovies);
    }
    return initialMovies;
  });

  const [selectedMovieId, setSelectedMovieId] = useState(null);

  // Save movies to local storage whenever they change
  useEffect(() => {
    localStorage.setItem('synthflixMovies', JSON.stringify(movies));
  }, [movies]);

  const selectedMovie = useMemo(() => {
    return movies.find(m => m.id === selectedMovieId) || null;
  }, [movies, selectedMovieId]);

  const handleAddMovie = (newMovie) => {
    setMovies(prevMovies => 
      [...prevMovies, newMovie].sort((a, b) => a.title.localeCompare(b.title))
    );
  };

  const handleUpdateMovie = (id, newPosterUrl) => {
    setMovies(prevMovies =>
      prevMovies.map(movie =>
        movie.id === id ? { ...movie, poster: newPosterUrl } : movie
      )
    );
  };

  return (
    <div className="bg-black min-h-screen">
      <style>{`
        /* Minimal scrollbar for Webkit */
        .scrollbar-thin::-webkit-scrollbar {
          height: 8px;
        }
        .scrollbar-thin::-webkit-scrollbar-thumb {
          background-color: #52525b; /* zinc-600 */
          border-radius: 10px;
        }
        .scrollbar-thin::-webkit-scrollbar-track {
          background-color: transparent;
        }
        /* Minimal scrollbar for Firefox */
        .scrollbar-thin {
          scrollbar-width: thin;
          scrollbar-color: #52525b transparent; /* zinc-600 transparent */
        }
      `}</style>
      
      <Header onNavigate={setActivePage} activePage={activePage} />
      
      <main>
        {activePage === 'browse' && (
          <BrowsePage movies={movies} onSelectMovie={setSelectedMovieId} />
        )}
        {activePage === 'upload' && (
          <UploadPage onAddMovie={handleAddMovie} onNavigate={setActivePage} />
        )}
      </main>

      {selectedMovie && (
        <EditModal
          movie={selectedMovie}
          onClose={() => setSelectedMovieId(null)}
          onSave={handleUpdateMovie}
        />
      )}
    </div>
  );
}


