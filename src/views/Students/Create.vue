<template>
  <div class="container mt-5">
    <div class="card">
      <div class="card-header">
        <h4>Add Students</h4>
      </div>
      <div class="card-body">
        <!-- Success Message -->
        <div v-if="successMessage" class="alert alert-success">
          {{ successMessage }}
        </div>

        <!-- Error List Display -->
        <ul class="alert alert-warning" v-if="errorList.length > 0">
          <li
            class="mb-0 ms-3"
            v-for="(error, index) in errorList"
            :key="index"
          >
            {{ error }}
          </li>
        </ul>

        <!-- Student Form -->
        <div class="mb-3">
          <label for="name">Name</label>
          <input
            type="text"
            v-model="model.student.name"
            class="form-control"
          />
        </div>
        <div class="mb-3">
          <label for="course">Course</label>
          <input
            type="text"
            v-model="model.student.course"
            class="form-control"
          />
        </div>
        <div class="mb-3">
          <label for="email">Email</label>
          <input
            type="email"
            v-model="model.student.email"
            class="form-control"
          />
        </div>
        <div class="mb-3">
          <label for="phone">Phone</label>
          <input
            type="text"
            v-model="model.student.phone"
            class="form-control"
          />
        </div>
        <div class="mb-3">
          <button type="button" class="btn btn-primary" @click="createStudent">
            Save
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "studentCreate",
  data() {
    return {
      errorList: [], // Initialize as an empty array instead of "null"
      successMessage: "", // Store success message
      model: {
        student: {
          name: "",
          course: "",
          email: "",
          phone: "",
        },
      },
    };
  },

  methods: {
    createStudent() {
      this.successMessage = ""; // Clear previous success message before making the request

      axios
        .post("http://127.0.0.1:8000/api/students", this.model.student)
        .then((res) => {
          if (res.data.status === 200) {
            // Set success message
            this.successMessage = res.data.message;
            this.errorList = []; // Clear previous errors if any

            // Reset the form after successful submission
            this.model.student = {
              name: "",
              course: "",
              email: "",
              phone: "",
            };
          }
        })
        .catch((error) => {
          if (error.response) {
            if (error.response.status === 422) {
              // Handle validation errors
              this.errorList = error.response.data.errors;
            } else if (error.response.status === 500) {
              // Handle server error
              this.successMessage = "Something went wrong.";
            }
          } else if (error.request) {
            console.log(error.request);
          } else {
            console.log("Error", error.message);
          }
        });
    },
  },
};
</script>
