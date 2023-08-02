<template>
    <div>
      <Welcome />
      <div class="box-container" v-if="showBook">
        <div class="addnewbook">
          <button type="button" @click="addNewBook">
            Add new Book
          </button>
        </div>
        <div class="booklist">
          <h2>List Books</h2>
          <table class="table table-bordered">
            <thead>
              <tr>
                <th scope="col">sl.no</th>
                <th scope="col">Title</th>
                <th scope="col">Author</th>
                <th scope="col">Genre</th>
                <th scope="col">Availability</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(book,index) in listBooks" :key="book.id">
                <th scope="row">{{ index+1 }}</th>
                <td>{{ book.title }}</td>
                <td>{{ book.author }}</td>
                <td>{{ book.genre }}</td>
                <td>{{ book.available?'Available':'Not Available' }}</td>
                <td>
                  <button class="actionBtn" type="button" @click="updateBook(book,index)">Update</button>
                  <button class="actionBtn" type="button" @click="removeBook(book.id)">Remove</button>
                </td>
              </tr>
              <tr v-if="!listBooks || listBooks.length === 0">
                <td colspan="6">No Book Found</td>
              </tr>
            </tbody>
          </table>
          </div>
          </div>
          <form @submit.prevent="onSubmit" novalidate v-else-if="!showBook">
            <div class="box-container">
              <h2 class="heading">Books</h2>
              <div class="form-fields">
                <input name="title" v-model="book.title" type="text" placeholder="Title">
                <small class="error" v-for="(error, index) of v$.book.title.$errors" :key="index">
                  {{ formatModalName(error.$property) }} {{formatMessage(error.$message)}}
                </small>
              </div>
              <div class="form-fields">
                <input name="author" v-model="book.author" type="text" placeholder="Author">
                <small class="error" v-for="(error, index) of v$.book.author.$errors" :key="index">
                  {{ formatModalName(error.$property) }} {{formatMessage(error.$message)}}
                </small>
              </div>
              <div class="form-fields">
                <input name="genre" v-model="book.genre" type="text" placeholder="Genre">
                <small class="error" v-for="(error, index) of v$.book.genre.$errors" :key="index">
                  {{ formatModalName(error.$property) }} {{formatMessage(error.$message)}}
                </small>
              </div>
              <div class="form-fields">
                <label for="availability">Availability:</label>
                <input type="checkbox" id="availability" v-model="book.available">
              </div>
              <div class="form-fields">
                <button class="signIn" type="submit">Save</button>
                <button class="createaccount" type="button" @click="cancel">Cancel</button> 
              </div>
            </div>
          </form>
          </div>
  </template>

  <script>
  import { mapState, mapActions } from 'vuex'
  import Welcome from '@/components/Welcome'
  import useVuelidate from '@vuelidate/core'
  import { required, minLength } from '@vuelidate/validators'
  import { capitalizeFirstLetter, lowercaseFirstLetter } from '@/config/Utils'  
  export default {
    name: 'todoPage'
    components : {
      Welcome,
    },
    setup() {
      return { v$: useVuelidate() }
    },
    data() {
      return {
        showBook: true,
        book: {
          title: '',
          author: '',
          genre: '',
          available: true,
        },
      }
    },
    validations() {
      return {
        book: {
          title: { required },
          author: { required },
          genre: { required },

        },
      }
    },
    computed: mapState({
      listBooks: (state) => state.library.listBooks,
    }),
    created () {
      this.$store.dispatch('library/getListBooks')
    },
    methods: {
      ...mapActions('library', ['addBook', 'updateBook', 'removeBook']),
      addNewBook() {
        this.showBook = false
      },
      formatModalName(string) {
        return capitalizeFirstLetter(string)
      },
      formatMessage(string) {
        return lowercaseFirstLetter(string)
      },
      onSubmit() {
        this.v$.$touch()
        if (this.v$.$error) return
        if (this.isEdit) {
          this.updateBook(this.book)
        } else {
          this.addBook(this.book)
        }
        this.reset()
      },
      updateBook(book) {
        if (this.isEdit) {
          this.showBook = false
          this.book = Object.assign({}, book)
        }
      },
      removeBook(id) {
        if (confirm('Are you sure you want to remove this book?')) {
          this.removeBook(id)
        }
      },
      cancel() {
        this.reset()
      },
      reset() {
        this.isEdit = false
        this.book = {
          title: '',
          author: '',
          genre: '',
          available: true,
        }
        this.showBook = true
      },
    },
  }
  </script>
  
  <style scoped>
  
  </style>