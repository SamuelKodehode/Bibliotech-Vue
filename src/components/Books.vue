<script lang="ts" setup>
import { onMounted, ref } from 'vue'
import Book from './Book.vue'
import { BookType } from '../helpers/types.ts'

const books = ref<BookType[]>([])
const users = ref<BookType[]>([])
const author = ref('')
const title = ref('')
const year = ref()

const fetchBooks = async () => {
    try {
        const response = await fetch('http://localhost:5000/book', {
            mode: 'cors'
        })

        const data = await response.json()
        console.log('Data:', data)
        books.value = data
    } catch (error) {
        console.error('Error fetching books:', error)
    }
}

const fetchUsers = async () => {
    try {
        const response = await fetch('http://localhost:5000/users', {
            mode: 'cors'
        })

        const data = await response.json()
        console.log('Data:', data)
        users.value = data
    } catch (error) {
        console.error('Error fetching books:', error)
    }
}

onMounted(async () => {
    await fetchBooks()
    await fetchUsers()
})

const postBook = async () => {
    const newBook = {
        id: Math.random() * 10,
        title: title.value.toUpperCase(),
        author: author.value.toUpperCase(),
        year: parseInt(year.value)
    }

    try {
        const response = await fetch('http://localhost:5000/book', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(newBook)
        })

        if (response.ok) {
            await fetchBooks()
            title.value = ''
            author.value = ''
            year.value = ''
        } else {
            console.error('Failed to add book')
        }
    } catch (error) {
        console.error('Error adding book:', error)
    }
}
</script>

<template>
    <div>
        <input v-model="title" placeholder="Title" type="text" />
        <input v-model="author" placeholder="Author" type="text" />
        <input v-model="year" placeholder="Year" type="number" />
        <button @click="postBook">Add Book</button>
    </div>
    <div id="book-wrapper">
        <div id="book" v-for="(book, index) in books" :key="book.id">
            <Book
                :id="book.book_id"
                :author="book.author"
                :year="book.year"
                :title="book.title"
                :user="users[index]"
                :book="book"
                :fetch-books="fetchBooks"
            />
        </div>
    </div>
</template>

<style>
#book-wrapper {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 1fr;
    grid-gap: 1rem;
    color: aliceblue;
}

#book {
    background-color: rgba(17, 17, 17, 0.24);
}
</style>
