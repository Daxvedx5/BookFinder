<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BookFinder - Busca y lee libros</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        .book-cover {
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }
        .book-cover:hover {
            transform: scale(1.05);
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
        }
        .reading-pane {
            height: calc(100vh - 200px);
            overflow-y: auto;
            background-color: #f8f4e8;
            padding: 2rem;
            border-radius: 8px;
            box-shadow: inset 0 0 10px rgba(0, 0, 0, 0.1);
        }
        .loader {
            border-top-color: #3b82f6;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
</head>
<body class="bg-gray-100 min-h-screen">
    <!-- Header -->
    <header class="bg-gradient-to-r from-blue-600 to-indigo-800 text-white shadow-lg">
        <div class="container mx-auto px-4 py-6">
            <div class="flex flex-col md:flex-row items-center justify-between">
                <div class="flex items-center mb-4 md:mb-0">
                    <i class="fas fa-book-open text-3xl mr-3"></i>
                    <h1 class="text-3xl font-bold">BookFinder</h1>
                </div>
                <div class="relative w-full md:w-1/2">
                    <form id="search-form" class="flex">
                        <input 
                            type="text" 
                            id="search-input" 
                            placeholder="Busca libros por título, autor o ISBN..." 
                            class="w-full px-4 py-3 rounded-l-lg focus:outline-none text-gray-800"
                        >
                        <button 
                            type="submit" 
                            class="bg-amber-500 hover:bg-amber-600 text-white px-6 py-3 rounded-r-lg transition duration-300"
                        >
                            <i class="fas fa-search"></i> Buscar
                        </button>
                    </form>
                </div>
            </div>
        </div>
    </header>

    <!-- Main Content -->
    <main class="container mx-auto px-4 py-8">
        <div id="search-results" class="mb-8">
            <!-- Results will be displayed here -->
        </div>

        <div id="book-details" class="hidden">
            <div class="flex justify-between items-center mb-6">
                <h2 id="book-title" class="text-3xl font-bold text-gray-800"></h2>
                <button 
                    id="back-to-results" 
                    class="bg-gray-200 hover:bg-gray-300 text-gray-800 px-4 py-2 rounded-lg transition duration-300 flex items-center"
                >
                    <i class="fas fa-arrow-left mr-2"></i> Volver a los resultados
                </button>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
                <div class="lg:col-span-1 bg-white p-6 rounded-xl shadow-md">
                    <img 
                        id="book-cover-large" 
                        src="" 
                        alt="Portada del libro" 
                        class="w-full h-auto max-h-96 object-contain mx-auto book-cover mb-4"
                    >
                    <div class="space-y-4">
                        <p class="text-gray-600"><span class="font-semibold">Autor:</span> <span id="book-author"></span></p>
                        <p class="text-gray-600"><span class="font-semibold">Publicado:</span> <span id="book-published"></span></p>
                        <p class="text-gray-600"><span class="font-semibold">Editorial:</span> <span id="book-publisher"></span></p>
                        <p class="text-gray-600"><span class="font-semibold">Páginas:</span> <span id="book-pages"></span></p>
                        <div id="book-buy-links" class="flex flex-wrap gap-2 mt-4"></div>
                        <button 
                            id="read-book-btn" 
                            class="w-full bg-blue-600 hover:bg-blue-700 text-white py-3 px-4 rounded-lg transition duration-300 mt-4 flex items-center justify-center"
                        >
                            <i class="fas fa-book-reader mr-2"></i> Leer fragmento
                        </button>
                    </div>
                </div>

                <div class="lg:col-span-2">
                    <h3 class="text-xl font-semibold mb-4 text-gray-800">Descripción</h3>
                    <div class="bg-white p-6 rounded-xl shadow-md mb-6">
                        <p id="book-description" class="text-gray-700 leading-relaxed"></p>
                    </div>
                    
                    <div id="read-view" class="hidden">
                        <h3 class="text-xl font-semibold mb-4 text-gray-800">Lector</h3>
                        <div class="bg-white p-6 rounded-xl shadow-md">
                            <div id="book-content" class="reading-pane">
                                <p id="book-text" class="text-gray-800 leading-relaxed text-lg"></p>
                            </div>
                            <div class="flex justify-between mt-4">
                                <button 
                                    id="prev-page" 
                                    class="bg-blue-100 hover:bg-blue-200 text-blue-800 px-4 py-2 rounded-lg transition duration-300 disabled:opacity-50 disabled:cursor-not-allowed"
                                    disabled
                                >
                                    <i class="fas fa-chevron-left mr-2"></i>Atrás
                                </button>
                                <span id="page-info" class="text-gray-600">Página 1 de X</span>
                                <button 
                                    id="next-page" 
                                    class="bg-blue-100 hover:bg-blue-200 text-blue-800 px-4 py-2 rounded-lg transition duration-300 disabled:opacity-50 disabled:cursor-not-allowed"
                                >
                                    Siguiente<i class="fas fa-chevron-right ml-2"></i>
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div id="loader" class="hidden fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
            <div class="loader ease-linear rounded-full border-4 border-t-4 border-gray-200 h-12 w-12"></div>
        </div>
    </main>

    <!-- Footer -->
    <footer class="bg-gray-800 text-white py-8 mt-12">
        <div class="container mx-auto px-4">
            <div class="flex flex-col md:flex-row justify-between items-center">
                <div class="mb-4 md:mb-0">
                    <h3 class="text-xl font-bold">BookFinder</h3>
                    <p class="text-gray-400 mt-2">Encuentra y lee tus libros favoritos</p>
                </div>
                <div class="flex space-x-4">
                    <a href="#" class="text-gray-400 hover:text-white transition duration-300">
                        <i class="fab fa-twitter text-xl"></i>
                    </a>
                    <a href="#" class="text-gray-400 hover:text-white transition duration-300">
                        <i class="fab fa-facebook text-xl"></i>
                    </a>
                    <a href="#" class="text-gray-400 hover:text-white transition duration-300">
                        <i class="fab fa-instagram text-xl"></i>
                    </a>
                </div>
            </div>
            <div class="border-t border-gray-700 mt-6 pt-6 text-center text-gray-400">
                <p>&copy; 2023 BookFinder - Todos los derechos reservados</p>
            </div>
            <p class="text-xs text-gray-500 text-center mt-4">
                Los datos de los libros son proporcionados por la API de Google Books
            </p>
        </div>
    </footer>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            // Elements
            const searchForm = document.getElementById('search-form');
            const searchInput = document.getElementById('search-input');
            const searchResults = document.getElementById('search-results');
            const bookDetails = document.getElementById('book-details');
            const backToResults = document.getElementById('back-to-results');
            const loader = document.getElementById('loader');
            
            // Book details elements
            const bookTitle = document.getElementById('book-title');
            const bookCoverLarge = document.getElementById('book-cover-large');
            const bookAuthor = document.getElementById('book-author');
            const bookPublished = document.getElementById('book-published');
            const bookPublisher = document.getElementById('book-publisher');
            const bookPages = document.getElementById('book-pages');
            const bookDescription = document.getElementById('book-description');
            const bookBuyLinks = document.getElementById('book-buy-links');
            const readBookBtn = document.getElementById('read-book-btn');
            const readView = document.getElementById('read-view');
            const bookText = document.getElementById('book-text');
            const prevPageBtn = document.getElementById('prev-page');
            const nextPageBtn = document.getElementById('next-page');
            const pageInfo = document.getElementById('page-info');

            // Variables
            let currentBook = null;
            let currentSearchResults = [];
            let currentPage = 1;
            let totalPages = 1;
            let paragraphs = [];

            // Event Listeners
            searchForm.addEventListener('submit', handleSearch);
            backToResults.addEventListener('click', showResults);
            readBookBtn.addEventListener('click', showReadView);
            prevPageBtn.addEventListener('click', goToPrevPage);
            nextPageBtn.addEventListener('click', goToNextPage);

            // Functions
            async function handleSearch(e) {
                e.preventDefault();
                const query = searchInput.value.trim();
                
                if (!query) {
                    showAlert('Por favor, introduce un término de búsqueda', 'error');
                    return;
                }
                
                showLoader();
                searchResults.innerHTML = '';
                
                try {
                    const books = await searchBooks(query);
                    currentSearchResults = books;
                    
                    if (books.length === 0) {
                        showAlert('No se encontraron libros con ese término de búsqueda', 'info');
                        hideLoader();
                        return;
                    }
                    
                    displaySearchResults(books);
                    hideLoader();
                } catch (error) {
                    console.error('Error al buscar libros:', error);
                    showAlert('Error al buscar libros. Por favor, inténtalo de nuevo.', 'error');
                    hideLoader();
                }
            }

            async function searchBooks(query) {
                const apiUrl = https://www.googleapis.com/books/v1/volumes?q=${encodeURIComponent(query)}&maxResults=20&printType=books;
                const response = await fetch(apiUrl);
                const data = await response.json();
                
                if (!data.items) {
                    return [];
                }
                
                return data.items.map(item => {
                    const volumeInfo = item.volumeInfo || {};
                    const accessInfo = item.accessInfo || {};
                    
                    return {
                        id: item.id,
                        title: volumeInfo.title || 'Título desconocido',
                        authors: volumeInfo.authors || ['Autor desconocido'],
                        publisher: volumeInfo.publisher || 'Editorial desconocida',
                        publishedDate: volumeInfo.publishedDate || 'Fecha desconocida',
                        description: volumeInfo.description || 'No hay descripción disponible.',
                        pageCount: volumeInfo.pageCount || '--',
                        categories: volumeInfo.categories || [],
                        averageRating: volumeInfo.averageRating,
                        ratingsCount: volumeInfo.ratingsCount,
                        imageLinks: volumeInfo.imageLinks || {},
                        industryIdentifiers: volumeInfo.industryIdentifiers || [],
                        previewLink: volumeInfo.previewLink,
                        infoLink: volumeInfo.infoLink,
                        saleInfo: item.saleInfo || {},
                        accessInfo: accessInfo
                    };
                });
            }

            function displaySearchResults(books) {
                searchResults.innerHTML = `
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-800">
                            Resultados de búsqueda (${books.length})
                        </h2>
                    </div>
                    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
                        ${books.map(book => createBookCard(book)).join('')}
                    </div>
                `;
                
                // Add event listeners to book cards
                document.querySelectorAll('.book-card').forEach(card => {
                    card.addEventListener('click', () => showBookDetails(card.dataset.bookId));
                });
            }

            function createBookCard(book) {
                const coverUrl = book.imageLinks.thumbnail || 'https://via.placeholder.com/150x200?text=No+portada';
                
                return `
                    <div class="book-card cursor-pointer" data-book-id="${book.id}">
                        <div class="bg-white rounded-lg shadow-md overflow-hidden h-full flex flex-col transition duration-300 hover:shadow-xl">
                            <img 
                                src="${coverUrl}" 
                                alt="${book.title}" 
                                class="w-full h-48 object-cover"
                                onerror="this.src='https://via.placeholder.com/150x200?text=No+portada'"
                            >
                            <div class="p-4 flex flex-col flex-grow">
                                <h3 class="font-bold text-lg text-gray-800 mb-2 line-clamp-2">${book.title}</h3>
                                <p class="text-gray-600 text-sm mb-2">${book.authors.join(', ')}</p>
                                <div class="mt-auto">
                                    ${book.averageRating ? `
                                        <div class="flex items-center text-yellow-500 text-sm mb-2">
                                            ${'★'.repeat(Math.round(book.averageRating))}${'☆'.repeat(5 - Math.round(book.averageRating))}
                                            <span class="text-gray-500 ml-1">(${book.ratingsCount || 0})</span>
                                        </div>
                                    ` : ''}
                                    <span class="text-xs text-gray-500">${book.publishedDate.split('-')[0] || ''}</span>
                                </div>
                            </div>
                        </div>
                    </div>
                `;
            }

            async function showBookDetails(bookId) {
                showLoader();
                
                const book = currentSearchResults.find(b => b.id === bookId);
                if (!book) {
                    hideLoader();
                    return;
                }
                
                currentBook = book;
                
                // Update book details
                bookTitle.textContent = book.title;
                bookCoverLarge.src = book.imageLinks.thumbnail || 'https://via.placeholder.com/300x450?text=No+portada';
                bookCoverLarge.alt = book.title;
                bookAuthor.textContent = book.authors.join(', ');
                bookPublished.textContent = formatDate(book.publishedDate);
                bookPublisher.textContent = book.publisher;
                bookPages.textContent = book.pageCount || '--';
                bookDescription.textContent = book.description;
                
                // Clear and add buy links
                bookBuyLinks.innerHTML = '';
                
                if (book.saleInfo && book.saleInfo.buyLink) {
                    const buyLink = document.createElement('a');
                    buyLink.href = book.saleInfo.buyLink;
                    buyLink.target = '_blank';
                    buyLink.className = 'bg-amber-500 hover:bg-amber-600 text-white px-4 py-2 rounded-lg transition duration-300 inline-flex items-center';
                    buyLink.innerHTML = '<i class="fas fa-shopping-cart mr-2"></i> Comprar';
                    bookBuyLinks.appendChild(buyLink);
                }
                
                const moreInfoLink = document.createElement('a');
                moreInfoLink.href = book.infoLink;
                moreInfoLink.target = '_blank';
                moreInfoLink.className = 'bg-gray-200 hover:bg-gray-300 text-gray-800 px-4 py-2 rounded-lg transition duration-300 inline-flex items-center';
                moreInfoLink.innerHTML = '<i class="fas fa-info-circle mr-2"></i> Más info';
                bookBuyLinks.appendChild(moreInfoLink);
                
                // Reset reading view
                readView.classList.add('hidden');
                readBookBtn.innerHTML = '<i class="fas fa-book-reader mr-2"></i> Leer fragmento';
                
                // Prepare book content for reading
                paragraphs = [];
                if (book.description) {
                    paragraphs = book.description.split('\n').filter(p => p.trim());
                }
                
                if (paragraphs.length > 0) {
                    totalPages = Math.max(Math.ceil(paragraphs.length / 3), 1);
                    showPage(1);
                } else {
                    bookText.textContent = "No hay un fragmento disponible para leer.";
                    prevPageBtn.disabled = true;
                    nextPageBtn.disabled = true;
                }
                
                // Show details and hide results
                bookDetails.classList.remove('hidden');
                searchResults.classList.add('hidden');
                
                hideLoader();
                window.scrollTo({ top: 0, behavior: 'smooth' });
            }

            function showResults() {
                searchResults.classList.remove('hidden');
                bookDetails.classList.add('hidden');
                window.scrollTo({ top: 0, behavior: 'smooth' });
                currentBook = null;
            }

            function showReadView() {
                if (readView.classList.contains('hidden')) {
                    if (paragraphs.length === 0) {
                        showAlert('No hay un fragmento disponible para leer.', 'info');
                        return;
                    }
                    
                    readView.classList.remove('hidden');
                    readBookBtn.innerHTML = '<i class="fas fa-eye-slash mr-2"></i> Ocultar lector';
                    window.scrollTo({ top: document.getElementById('book-content').offsetTop - 20, behavior: 'smooth' });
                } else {
                    readView.classList.add('hidden');
                    readBookBtn.innerHTML = '<i class="fas fa-book-reader mr-2"></i> Leer fragmento';
                }
            }

            function showPage(pageNum) {
                if (pageNum < 1 || pageNum > totalPages || !currentBook) return;
                
                currentPage = pageNum;
                const startIdx = (pageNum - 1) * 3;
                const endIdx = Math.min(startIdx + 3, paragraphs.length);
                const pageContent = paragraphs.slice(startIdx, endIdx).join('\n\n');
                
                bookText.textContent = pageContent;
                pageInfo.textContent = Página ${pageNum} de ${totalPages};
                
                prevPageBtn.disabled = pageNum === 1;
                nextPageBtn.disabled = pageNum === totalPages;
            }

            function goToPrevPage() {
                if (currentPage > 1) {
                    showPage(currentPage - 1);
                    document.getElementById('book-content').scrollTo(0, 0);
                }
            }

            function goToNextPage() {
                if (currentPage < totalPages) {
                    showPage(currentPage + 1);
                    document.getElementById('book-content').scrollTo(0, 0);
                }
            }

            function showLoader() {
                loader.classList.remove('hidden');
            }

            function hideLoader() {
                loader.classList.add('hidden');
            }

            function showAlert(message, type) {
                const alert = document.createElement('div');
                alert.className = `fixed top-4 right-4 px-6 py-4 rounded-lg shadow-lg z-50 ${
                    type === 'error' ? 'bg-red-500 text-white' : 
                    type === 'info' ? 'bg-blue-500 text-white' : 
                    'bg-gray-800 text-white'
                }`;
                alert.innerHTML = `
                    <div class="flex items-center">
                        <i class="fas ${type === 'error' ? 'fa-exclamation-circle' : 'fa-info-circle'} mr-3"></i>
                        <span>${message}</span>
                        <button class="ml-4" onclick="this.parentElement.parentElement.remove()">
                            <i class="fas fa-times"></i>
                        </button>
                    </div>
                `;
                
                document.body.appendChild(alert);
                
                setTimeout(() => {
                    alert.remove();
                }, 5000);
            }

            function formatDate(dateString) {
                if (!dateString) return 'Fecha desconocida';
                
                const date = new Date(dateString);
                if (isNaN(date.getTime())) return dateString;
                
                return date.toLocaleDateString('es-ES', {
                    year: 'numeric',
                    month: 'long',
                    day: 'numeric'
                });
            }
        });
    </script>
</body>
</html>
