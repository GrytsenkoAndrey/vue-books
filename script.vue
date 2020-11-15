new Vue({
    el: '#bookSearchApp',
    data: {
        searchTerm: '',
        searchResults: [],
        books: [
            {title: '', publisher: '', published: ''},
        ],
        savedBooks: [
            {title: '', publisher: '', published: ''},
        ]
    },
    methods: {
        search() {
            axios.get(`https://www.googleapis.com/books/v1/volumes?q=` + this.searchTerm)
            .then(response => {
                this.searchResults = response.data
            })
            .catch(e => {
                console.log(e)
            });

            this.books = JSON.parse(localStorage.getItem("favoriteBooks"));
        },

        bookAuthors(book) {
            let authors = book.volumeInfo.authors;
            if (authors.length < 3) {
                authors = authors.join(' and ')
            }
            else if (authors.length > 2) {
                let lastAuthor = ' and ' + authors.slice(-1);
                authors.pop()
                authors = authors.join(', ')
                authors += lastAuthor
            }
            return authors
        },

        addBook(book) {
            
            if (localStorage.favoriteBooks) {
                this.savedBooks = JSON.parse(localStorage.getItem("favoriteBooks"));
                this.savedBooks.push({
                    title: book.volumeInfo.title,
                    publisher: book.volumeInfo.publisher,
                    published: book.volumeInfo.publishedDate
                });
                this.books = this.savedBooks;
            } else {
                this.books.push({
                    title: book.volumeInfo.title,
                    publisher: book.volumeInfo.publisher,
                    published: book.volumeInfo.publishedDate
                });
            }
            
            localStorage.setItem("favoriteBooks", JSON.stringify(this.books));
        },

        deleteItem: function (index) {
            this.books.splice(index, 1);

            localStorage.removeItem("favoriteBooks");
            localStorage.setItem("favoriteBooks", JSON.stringify(this.books));
        }
    }
});
