<template>
  <div class="container mt-5">
    <h2 class="mb-4">Employee Management System</h2>
    
    <!-- Add/Edit Employee Form -->
    <div class="card mb-4">
      <div class="card-header bg-primary text-white">
        {{ isEditing ? 'Edit Employee' : 'Add New Employee' }}
      </div>
      <div class="card-body">
        <form @submit.prevent="submitForm">
          <div class="row">
            <div class="col-md-6 mb-3">
              <label for="empId" class="form-label">Employee ID</label>
              <input type="text" class="form-control" id="empId" v-model="form.empId" required>
            </div>
            <div class="col-md-6 mb-3">
              <label for="name" class="form-label">Name</label>
              <input type="text" class="form-control" id="name" v-model="form.name" required>
            </div>
            <div class="col-md-6 mb-3">
              <label for="designation" class="form-label">Designation</label>
              <input type="text" class="form-control" id="designation" v-model="form.designation" required>
            </div>
            <div class="col-md-6 mb-3">
              <label for="department" class="form-label">Department</label>
              <input type="text" class="form-control" id="department" v-model="form.department" required>
            </div>
            <div class="col-md-6 mb-3">
              <label for="salary" class="form-label">Salary</label>
              <input type="number" class="form-control" id="salary" v-model="form.salary" required>
            </div>
          </div>
          <button type="submit" class="btn" :class="isEditing ? 'btn-success' : 'btn-primary'">
            {{ isEditing ? 'Update Employee' : 'Add Employee' }}
          </button>
          <button type="button" class="btn btn-secondary ms-2" v-if="isEditing" @click="resetForm">
            Cancel
          </button>
        </form>
      </div>
    </div>

    <!-- Employee List Table -->
    <div class="card">
      <div class="card-header bg-dark text-white">
        Employee Directory
      </div>
      <div class="card-body p-0">
        <div class="table-responsive">
          <table class="table table-striped table-hover mb-0">
            <thead class="table-light">
              <tr>
                <th>Employee ID</th>
                <th>Name</th>
                <th>Designation</th>
                <th>Department</th>
                <th>Salary</th>
                <th>Actions</th>
              </tr>
            </thead>
            <tbody>
              <tr v-if="loading">
                <td colspan="6" class="text-center py-4">Loading employees...</td>
              </tr>
              <tr v-else-if="employees.length === 0">
                <td colspan="6" class="text-center py-4">No employees found.</td>
              </tr>
              <tr v-else v-for="emp in employees" :key="emp.empId || emp.id">
                <td>{{ emp.empId }}</td>
                <td>{{ emp.name }}</td>
                <td>{{ emp.designation }}</td>
                <td>{{ emp.department }}</td>
                <td>${{ emp.salary }}</td>
                <td>
                  <button class="btn btn-sm btn-warning me-2" @click="editEmployee(emp)">Edit</button>
                  <button class="btn btn-sm btn-danger" @click="deleteEmployee(emp.empId || emp.id)">Delete</button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

// ⚠️ IMPORTANT: Replace this URL with your actual MockAPI endpoint URL!
const API_URL = 'https://6a00e08936fb6ad04de08da4.mockapi.io/v1/dets'; // Updated to point to the 'dets' resource

export default {
  name: 'EmployeeManagement',
  data() {
    return {
      employees: [],
      loading: false,
      isEditing: false,
      editId: null,
      form: {
        empId: '',
        name: '',
        designation: '',
        department: '',
        salary: ''
      }
    };
  },
  mounted() {
    this.fetchEmployees();
  },
  methods: {
    async fetchEmployees() {
      this.loading = true;
      try {
        const response = await axios.get(API_URL);
        this.employees = response.data;
      } catch (error) {
        console.error('Error fetching employees:', error);
        alert('Failed to fetch employees. Please check your MockAPI URL.');
      } finally {
        this.loading = false;
      }
    },
    async submitForm() {
      try {
        if (this.isEditing) {
          // Update existing employee
          await axios.put(`${API_URL}/${this.editId}`, this.form);
          alert('Employee updated successfully!');
        } else {
          // Add new employee
          await axios.post(API_URL, this.form);
          alert('Employee added successfully!');
        }
        this.resetForm();
        this.fetchEmployees();
      } catch (error) {
        console.error('Error saving employee:', error);
        alert('Failed to save employee. Check console for details.');
      }
    },
    editEmployee(emp) {
      this.isEditing = true;
      this.editId = emp.empId || emp.id;
      // Copy data to form
      this.form = {
        empId: emp.empId,
        name: emp.name,
        designation: emp.designation,
        department: emp.department,
        salary: emp.salary
      };
    },
    async deleteEmployee(id) {
      if (confirm('Are you sure you want to delete this employee?')) {
        try {
          await axios.delete(`${API_URL}/${id}`);
          alert('Employee deleted successfully!');
          this.fetchEmployees();
        } catch (error) {
          console.error('Error deleting employee:', error);
          alert('Failed to delete employee.');
        }
      }
    },
    resetForm() {
      this.isEditing = false;
      this.editId = null;
      this.form = {
        empId: '',
        name: '',
        designation: '',
        department: '',
        salary: ''
      };
    }
  }
};
</script>

<style scoped>
.container {
  max-width: 1000px;
}
</style>
