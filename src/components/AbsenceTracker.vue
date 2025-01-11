<template>
    <div class="student-tracker">
        <h2 class="title">School Name: {{ selectedSchool }}</h2>
        <div class="filter-container">
            <label for="schoolSelect">Select School:</label>
            <select id="schoolSelect" v-model="selectedSchool" class="dropdown">
                <option v-for="school in schoolNames" :key="school" :value="school">{{ school }}</option>
            </select>
        </div>
        <h3>Student List:</h3>
        <ul class="student-list">
            <li v-for="student in filteredStudents" :key="student.id">
                {{ student.studentName }} (Absence Length: {{ student.absenceLength }})
            </li>
        </ul>
        <p v-if="error" class="error-message">{{ error }}</p>
    </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';
import axios from 'axios';

export default defineComponent({
    setup() {
        const students = ref([]);
        const randomStudents = ref([]);
        const error = ref('');

        onMounted(async () => {
            try {
                const response = await axios.get('https://localhost:7173/api/Student/students');
                students.value = response.data; // Ensure data is correctly typed and filtered
            } catch (e) {
                console.error('Error fetching students:', e);
                error.value = 'Error loading student data. Please try again later.';
                students.value = [];
            }
        });

        const getRandomStudents = () => {
            const numberOfRandomStudents = 5; // Number of students to display
            if (students.value.length > 0) {
                const selectedIndexes = new Set<number>();
                while (selectedIndexes.size < numberOfRandomStudents) {
                    const randomIndex = Math.floor(Math.random() * students.value.length);
                    selectedIndexes.add(randomIndex);
                }
                randomStudents.value = Array.from(selectedIndexes).map(index => students.value[index]);
            }
        };

        return { randomStudents, getRandomStudents, error };
    }
});
</script>

<style scoped>
.student-tracker {
    font-family: 'Arial, sans-serif';
    margin: 20px;
    padding: 20px;
    background-color: #f9f9f9;
    border-radius: 8px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.title {
    font-size: 24px;
    margin-bottom: 10px;
}

.filter-container {
    margin-bottom: 20px;
}

.dropdown {
    padding: 8px;
    font-size: 16px;
    border: 1px solid #ccc;
    border-radius: 4px;
    width: 100%;
    max-width: 300px;
}

.student-list {
    list-style-type: none;
    padding: 0;
}

.student-list li {
    background-color: #fff;
    margin-bottom: 8px;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
}

.error-message {
    color: red;
    font-size: 14px;
    margin-top: 10px;
}
</style>

