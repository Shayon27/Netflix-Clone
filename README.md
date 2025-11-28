<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Netflix Clone</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Netflix+Sans:wght@400;700&display=swap');

        body {
            font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
            background-color: #141414;
            color: white;
            overflow-x: hidden;
        }

        /* Hide scrollbar for Chrome, Safari and Opera */
        .no-scrollbar::-webkit-scrollbar {
            display: none;
        }

        /* Hide scrollbar for IE, Edge and Firefox */
        .no-scrollbar {
            -ms-overflow-style: none;  /* IE and Edge */
            scrollbar-width: none;  /* Firefox */
        }

        .netflix-input {
            background: #333;
            border-radius: 4px;
            border: 0;
            color: #fff;
            height: 50px;
            line-height: 50px;
            padding: 16px 20px 0;
            width: 100%;
        }

        .netflix-input:focus {
            background: #454545;
            outline: none;
        }

        .row-poster {
            transition: transform 450ms;
        }

        .row-poster:hover {
            transform: scale(1.08);
            opacity: 1;
        }

        .modal-overlay {
            background-color: rgba(0, 0, 0, 0.8);
            animation: fadeIn 0.3s ease-in-out;
        }

        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }

        .banner-gradient {
            background: linear-gradient(180deg, transparent, rgba(37,37,37,0.61), #111);
        }
    </style>
</head>
<body>

    <!-- LOGIN SCREEN -->
    <div id="login-screen" class="fixed inset-0 z-[100] bg-black bg-opacity-90 bg-[url('https://assets.nflxext.com/ffe/siteui/vlv3/f841d4c7-10e1-40af-bcae-07a3f8dc141a/f6d7434e-d6de-4185-a6d4-c77a2d08737b/US-en-20220502-popsignuptwoweeks-perspective_alpha_website_medium.jpg')] bg-cover bg-no-repeat bg-center">
        <div class="absolute inset-0 bg-black bg-opacity-60"></div>
        
        <!-- Login Header -->
        <div class="relative px-6 py-6 md:px-12">
            <img src="https://upload.wikimedia.org/wikipedia/commons/0/08/Netflix_2015_logo.svg" alt="Logo" class="h-8 md:h-12">
        </div>

        <!-- Login Form -->
        <div class="relative z-10 max-w-[450px] mx-auto mt-4 bg-black/75 p-16 rounded-lg min-h-[600px]">
            <h1 class="text-3xl font-bold mb-8">Sign In</h1>
            <form onsubmit="handleLogin(event)" class="flex flex-col gap-4">
                <div class="relative">
                    <input type="email" id="email" class="w-full bg-[#333] rounded px-5 py-4 text-white outline-none focus:bg-[#454545]" placeholder="Email or phone number" required>
                </div>
                <div class="relative">
                    <input type="password" id="password" class="w-full bg-[#333] rounded px-5 py-4 text-white outline-none focus:bg-[#454545]" placeholder="Password" required>
                </div>
                <button type="submit" class="w-full bg-[#e50914] text-white font-bold py-4 rounded mt-6 hover:bg-[#c11119] transition">Sign In</button>
                
                <div class="flex justify-between text-[#b3b3b3] text-sm mt-2">
                    <label class="flex items-center gap-2 cursor-pointer">
                        <input type="checkbox" class="accent-[#737373]"> Remember me
                    </label>
                    <a href="#" class="hover:underline">Need help?</a>
                </div>
            </form>

            <div class="mt-16 text-[#737373]">
                <p>New to Netflix? <a href="#" class="text-white hover:underline">Sign up now.</a></p>
                <p class="mt-4 text-xs">This page is protected by Google reCAPTCHA to ensure you're not a bot.</p>
                <button onclick="fillCredentials()" class="mt-4 text-sm text-white bg-gray-600 px-3 py-1 rounded">Fill Demo User</button>
            </div>
        </div>
    </div>

    <!-- MAIN APP (Hidden initially) -->
    <div id="app" class="hidden min-h-screen relative">
        
        <!-- Navbar -->
        <nav id="navbar" class="fixed top-0 w-full z-50 transition-all duration-300 bg-gradient-to-b from-black/80 to-transparent p-4 md:px-12">
            <div class="flex items-center justify-between">
                <div class="flex items-center gap-8">
                    <img src="https://upload.wikimedia.org/wikipedia/commons/0/08/Netflix_2015_logo.svg" alt="Logo" class="h-6 md:h-8 cursor-pointer">
                    <ul class="hidden md:flex gap-6 text-sm text-gray-200">
                        <li class="font-bold cursor-pointer">Home</li>
                        <li class="hover:text-gray-400 cursor-pointer transition">TV Shows</li>
                        <li class="hover:text-gray-400 cursor-pointer transition">Movies</li>
                        <li class="hover:text-gray-400 cursor-pointer transition">New & Popular</li>
                        <li class="hover:text-gray-400 cursor-pointer transition">My List</li>
                    </ul>
                </div>

                <div class="flex items-center gap-6 text-white text-lg">
                    <i class="fas fa-search cursor-pointer hover:text-gray-300"></i>
                    <span class="text-sm hidden md:block cursor-pointer">KIDS</span>
                    <i class="fas fa-bell cursor-pointer hover:text-gray-300"></i>
                    <div class="flex items-center gap-2 cursor-pointer group relative">
                        <img src="https://upload.wikimedia.org/wikipedia/commons/0/0b/Netflix-avatar.png" alt="User" class="w-8 h-8 rounded">
                        <i class="fas fa-caret-down transition-transform group-hover:rotate-180"></i>
                        <!-- Dropdown -->
                        <div class="absolute top-full right-0 mt-2 w-48 bg-black/90 border border-gray-700 rounded shadow-xl hidden group-hover:block">
                             <div class="py-2">
                                <a href="#" class="block px-4 py-2 text-sm text-gray-300 hover:text-white hover:underline">Manage Profiles</a>
                                <a href="#" class="block px-4 py-2 text-sm text-gray-300 hover:text-white hover:underline">Account</a>
                                <div class="border-t border-gray-700 my-1"></div>
                                <a onclick="logout()" class="block px-4 py-2 text-sm text-center text-white hover:underline cursor-pointer">Sign out</a>
                             </div>
                        </div>
                    </div>
                </div>
            </div>
        </nav>

        <!-- Hero Header -->
        <header id="banner" class="relative h-[85vh] text-white object-cover bg-center bg-cover" style="background-image: url('https://images.unsplash.com/photo-1626814026160-2237a95fc5a0?q=80&w=2070&auto=format&fit=crop');">
            <div class="absolute inset-0 bg-gradient-to-t from-[#141414] via-transparent to-black/30"></div>
            <div class="absolute inset-0 bg-gradient-to-r from-[#141414]/80 via-transparent to-transparent"></div>

            <div class="absolute top-[30%] left-4 md:left-12 max-w-2xl pt-8">
                <h1 id="banner-title" class="text-5xl md:text-6xl font-extrabold pb-4">Stranger Things</h1>
                <div class="flex items-center gap-4 mb-4 text-sm md:text-base font-bold text-gray-200">
                    <span class="text-[#46d369]">98% Match</span>
                    <span>2022</span>
                    <span class="border border-white/40 px-1 text-xs">TV-14</span>
                    <span>4 Seasons</span>
                </div>
                <p id="banner-desc" class="w-full md:max-w-[70%] text-lg md:text-xl font-medium drop-shadow-md text-white mb-6 leading-tight">
                    When a young boy vanishes, a small town uncovers a mystery involving secret experiments, terrifying supernatural forces, and one strange little girl.
                </p>
                
                <div class="flex gap-3">
                    <button class="flex items-center gap-2 bg-white text-black px-8 py-2 rounded hover:bg-white/80 transition font-bold text-lg">
                        <i class="fas fa-play"></i> Play
                    </button>
                    <button class="flex items-center gap-2 bg-[rgba(109,109,110,0.7)] text-white px-8 py-2 rounded hover:bg-[rgba(109,109,110,0.4)] transition font-bold text-lg">
                        <i class="fas fa-info-circle"></i> More Info
                    </button>
                </div>
            </div>
        </header>

        <!-- Rows Container -->
        <div id="rows-container" class="pl-4 md:pl-12 pb-20 -mt-24 relative z-10">
            <!-- Rows will be injected here by JS -->
        </div>

    </div>

    <!-- VIDEO/INFO MODAL -->
    <div id="modal" class="fixed inset-0 z-[100] hidden items-center justify-center modal-overlay p-4" onclick="closeModal(event)">
        <div class="bg-[#181818] w-full max-w-4xl rounded-lg overflow-hidden relative shadow-2xl transform transition-all" onclick="event.stopPropagation()">
            <!-- Close Button -->
            <button onclick="closeModal(event)" class="absolute top-4 right-4 z-20 bg-[#181818] rounded-full p-2 hover:bg-[#333]">
                <i class="fas fa-times text-white text-xl w-6 h-6 flex items-center justify-center"></i>
            </button>

            <!-- Video/Image Area -->
            <div class="relative h-[400px]">
                <img id="modal-banner" src="" class="w-full h-full object-cover opacity-60">
                <div class="absolute bottom-0 left-0 w-full h-1/2 bg-gradient-to-t from-[#181818] to-transparent"></div>
                
                <div class="absolute bottom-10 left-10">
                    <h2 id="modal-title" class="text-4xl font-bold mb-4">Movie Title</h2>
                    <div class="flex gap-4">
                        <button class="bg-white text-black px-8 py-2 rounded font-bold flex items-center gap-2 hover:bg-gray-200">
                            <i class="fas fa-play"></i> Play
                        </button>
                        <button class="border border-gray-500 rounded-full w-10 h-10 flex items-center justify-center hover:border-white">
                            <i class="fas fa-plus"></i>
                        </button>
                        <button class="border border-gray-500 rounded-full w-10 h-10 flex items-center justify-center hover:border-white">
                            <i class="far fa-thumbs-up"></i>
                        </button>
                    </div>
                </div>
            </div>

            <!-- Info Area -->
            <div class="p-10 flex flex-col md:flex-row gap-8">
                <div class="flex-1 text-gray-300">
                    <div class="flex items-center gap-4 mb-4 text-sm font-bold">
                        <span class="text-[#46d369]" id="modal-match">98% Match</span>
                        <span id="modal-year">2022</span>
                        <span class="border border-white/40 px-1 text-xs" id="modal-rating">TV-14</span>
                    </div>
                    <p id="modal-desc" class="text-base leading-relaxed text-white">
                        Description goes here.
                    </p>
                </div>
                <div class="w-full md:w-1/3 text-sm text-gray-400 space-y-2">
                    <p><span class="text-[#777]">Cast:</span> <span class="text-white hover:underline cursor-pointer">Unknown Actor</span>, <span class="text-white hover:underline cursor-pointer">Another Star</span></p>
                    <p><span class="text-[#777]">Genres:</span> <span id="modal-genres" class="text-white">Action, Sci-Fi</span></p>
                    <p><span class="text-[#777]">This movie is:</span> <span class="text-white">Exciting, Suspenseful</span></p>
                </div>
            </div>
        </div>
    </div>

    <!-- JAVASCRIPT LOGIC -->
    <script>
        // --- DATA ---
        const movies = [
            {
                title: "Stranger Things",
                desc: "When a young boy vanishes, a small town uncovers a mystery involving secret experiments, terrifying supernatural forces, and one strange little girl.",
                banner: "https://images.unsplash.com/photo-1626814026160-2237a95fc5a0?q=80&w=2070&auto=format&fit=crop",
                match: "98% Match", rating: "TV-14", duration: "4 Seasons",
                genres: ["Sci-Fi", "Horror"], category: 'trending'
            },
            {
                title: "The Dark Knight",
                desc: "Batman raises the stakes in his war on crime. With the help of Lt. Jim Gordon and DA Harvey Dent, Batman sets out to dismantle the remaining criminal organizations.",
                banner: "https://images.unsplash.com/photo-1478720568477-152d9b164e63?q=80&w=2000&auto=format&fit=crop",
                match: "99% Match", rating: "PG-13", duration: "2h 32m",
                genres: ["Action", "Crime"], category: 'top_rated'
            },
            {
                title: "Inception",
                desc: "A thief who steals corporate secrets through the use of dream-sharing technology is given the inverse task of planting an idea into the mind of a C.E.O.",
                banner: "https://images.unsplash.com/photo-1534447677768-be436bb09401?q=80&w=2000&auto=format&fit=crop",
                match: "96% Match", rating: "PG-13", duration: "2h 28m",
                genres: ["Sci-Fi", "Action"], category: 'action'
            },
            {
                title: "Space Odyssey",
                desc: "After discovering a mysterious artifact buried beneath the Lunar surface, mankind sets off on a quest to find its origins with help from intelligent supercomputer H.A.L. 9000.",
                banner: "https://images.unsplash.com/photo-1451187580459-43490279c0fa?q=80&w=2000&auto=format&fit=crop",
                match: "92% Match", rating: "G", duration: "2h 29m",
                genres: ["Sci-Fi", "Adventure"], category: 'scifi'
            },
            {
                title: "Cyberpunk City",
                desc: "In a dystopian future, a mercenary outlaw navigates a megalopolis obsessed with power, glamour and body modification.",
                banner: "https://images.unsplash.com/photo-1535295972055-1c762f4483e5?q=80&w=2000&auto=format&fit=crop",
                match: "89% Match", rating: "R", duration: "1h 55m",
                genres: ["Sci-Fi", "Action"], category: 'originals'
            },
            {
                title: "Laugh Track",
                desc: "A struggling comedian's life turns chaotic when he accidentally becomes the key witness to a mob hit, forcing him to go into hiding at a clown college.",
                banner: "https://images.unsplash.com/photo-1513151233558-d860c5398176?q=80&w=2000&auto=format&fit=crop",
                match: "85% Match", rating: "PG-13", duration: "1h 30m",
                genres: ["Comedy"], category: 'comedy'
            }
        ];

        // Duplicate movies to fill rows
        const allMovies = [...movies, ...movies, ...movies];

        const categories = [
            { id: 'trending', title: 'Trending Now' },
            { id: 'originals', title: 'Netflix Originals', isLarge: true },
            { id: 'top_rated', title: 'Top Rated' },
            { id: 'action', title: 'Action Thrillers' },
            { id: 'comedy', title: 'Comedies' },
            { id: 'scifi', title: 'Sci-Fi & Fantasy' },
        ];

        // --- APP LOGIC ---

        function handleLogin(e) {
            e.preventDefault();
            const email = document.getElementById('email').value;
            const pass = document.getElementById('password').value;
            
            if(email && pass) {
                document.getElementById('login-screen').style.opacity = '0';
                setTimeout(() => {
                    document.getElementById('login-screen').style.display = 'none';
                    document.getElementById('app').classList.remove('hidden');
                    initApp();
                }, 500);
            }
        }

        function fillCredentials() {
            document.getElementById('email').value = "user@netflix.com";
            document.getElementById('password').value = "password";
        }

        function logout() {
            location.reload();
        }

        function initApp() {
            renderRows();
            window.addEventListener('scroll', () => {
                const nav = document.getElementById('navbar');
                if (window.scrollY > 100) {
                    nav.classList.add('bg-black');
                    nav.classList.remove('bg-gradient-to-b');
                } else {
                    nav.classList.remove('bg-black');
                    nav.classList.add('bg-gradient-to-b');
                }
            });
        }

        function renderRows() {
            const container = document.getElementById('rows-container');
            
            categories.forEach(cat => {
                const rowTitle = document.createElement('h2');
                rowTitle.className = "text-xl md:text-2xl font-bold text-white mb-2 md:mb-4 px-2 hover:text-gray-300 transition cursor-pointer";
                rowTitle.innerText = cat.title;

                const rowWrapper = document.createElement('div');
                rowWrapper.className = "relative group mb-8 md:mb-12";

                const scrollContainer = document.createElement('div');
                scrollContainer.id = `slider-${cat.id}`;
                scrollContainer.className = "flex items-center gap-2 overflow-x-scroll no-scrollbar scroll-smooth px-2";

                // Mix random movies for demo
                const rowMovies = allMovies.sort(() => 0.5 - Math.random()).slice(0, 10);

                rowMovies.forEach(movie => {
                    const img = document.createElement('img');
                    img.src = movie.banner; // Using banner for standard rows
                    if(cat.isLarge) img.src = movie.banner; // Can use poster if available
                    
                    img.className = `cursor-pointer rounded-sm object-cover transition-transform duration-300 hover:scale-105 hover:z-10 ${cat.isLarge ? 'h-64 min-w-[180px]' : 'h-32 min-w-[200px]'}`;
                    img.onclick = () => openModal(movie);
                    scrollContainer.appendChild(img);
                });

                // Scroll Buttons
                const leftBtn = createScrollBtn('left', cat.id);
                const rightBtn = createScrollBtn('right', cat.id);

                rowWrapper.appendChild(leftBtn);
                rowWrapper.appendChild(scrollContainer);
                rowWrapper.appendChild(rightBtn);

                container.appendChild(rowTitle);
                container.appendChild(rowWrapper);
            });
        }

        function createScrollBtn(direction, id) {
            const btn = document.createElement('div');
            btn.className = `absolute top-0 bottom-0 ${direction === 'left' ? 'left-0' : 'right-0'} z-20 w-12 bg-black/50 hover:bg-black/70 flex items-center justify-center opacity-0 group-hover:opacity-100 transition duration-300 cursor-pointer h-full`;
            btn.innerHTML = `<i class="fas fa-chevron-${direction} text-white text-2xl"></i>`;
            btn.onclick = () => {
                const slider = document.getElementById(`slider-${id}`);
                const scrollAmount = window.innerWidth / 2;
                slider.scrollLeft = direction === 'left' 
                    ? slider.scrollLeft - scrollAmount 
                    : slider.scrollLeft + scrollAmount;
            };
            return btn;
        }

        // --- MODAL LOGIC ---
        function openModal(movie) {
            document.getElementById('modal-title').innerText = movie.title;
            document.getElementById('modal-desc').innerText = movie.desc;
            document.getElementById('modal-match').innerText = movie.match;
            document.getElementById('modal-rating').innerText = movie.rating;
            document.getElementById('modal-genres').innerText = movie.genres.join(', ');
            document.getElementById('modal-banner').src = movie.banner;
            
            const modal = document.getElementById('modal');
            modal.classList.remove('hidden');
            modal.classList.add('flex');
            document.body.style.overflow = 'hidden';
        }

        function closeModal(e) {
            // Close if clicked background or close button
            if (e.target.id === 'modal' || e.currentTarget.tagName === 'BUTTON') {
                const modal = document.getElementById('modal');
                modal.classList.add('hidden');
                modal.classList.remove('flex');
                document.body.style.overflow = 'auto';
            }
        }
    </script>
</body>

</html>
