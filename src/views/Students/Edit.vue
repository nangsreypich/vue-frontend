<template>
  <div class="container mt-5">
    <div class="card">
      <div class="card-header">
        <h4>Edit Students</h4>
      </div>
      <div class="card-body">
        <!-- Error List Display -->
        <ul class="alert alert-warning" v-if="errorList.length > 0">
          <li class="mb-0 ms-3" v-for="(error, index) in errorList" :key="index">
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
            id="name"
          />
        </div>
        <div class="mb-3">
          <label for="course">Course</label>
          <input
            type="text"
            v-model="model.student.course"
            class="form-control"
            id="course"
          />
        </div>
        <div class="mb-3">
          <label for="email">Email</label>
          <input
            type="email"
            v-model="model.student.email"
            class="form-control"
            id="email"
          />
        </div>
        <div class="mb-3">
          <label for="phone">Phone</label>
          <input
            type="text"
            v-model="model.student.phone"
            class="form-control"
            id="phone"
          />
        </div>
        <div class="mb-3">
          <button type="button" class="btn btn-primary" @click="updateStudent">
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
  name: "studentEdit",
  data() {
    return {
      studentId: "",
      errorList: [], // Correctly initialize as an empty array
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
  mounted() {
    this.studentId = this.$route.params.id;
    this.getStudentData(this.studentId);
  },
  methods: {
    getStudentData(studentId) {
      axios
        .get(`http://127.0.0.1:8000/api/students/${studentId}/edit`)
        .then((res) => {
          if (res.data && res.data.student) {
            this.model.student = res.data.student;
          }
        })
        .catch((error) => {
          if (error.response && error.response.data.message) {
            alert(error.response.data.message); // Alert only the API-provided message
          }
        });
    },
    updateStudent() {
      axios
        .put(
          `http://127.0.0.1:8000/api/students/${this.studentId}/edit`,
          this.model.student
        )
        .then((res) => {
          if (res.data.status === 200 && res.data.message) {
            alert(res.data.message); // Display API-provided success message
            this.errorList = []; // Clear errors on success
          }
        })
        .catch((error) => {
          if (error.response) {
            if (error.response.status === 422) {
              this.errorList = error.response.data.errors || [];
            } else if (error.response.data.message) {
              alert(error.response.data.message); // Alert only the API-provided message
            }
          }
        });
    },
  },
};
</script>
