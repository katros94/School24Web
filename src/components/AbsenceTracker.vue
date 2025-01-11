<template>
    <div class="student-tracker">
        <h2 class="title">Skola: {{ selectedSchool || 'Alla skolor' }}</h2>
        <div class="filter-container">
            <label for="schoolSelect">Välj skola</label>
            <select id="schoolSelect" v-model="selectedSchool" class="dropdown">
                <option value="">Alla skolor</option>
                <option v-for="school in schoolNames" :key="school" :value="school">{{ school }}</option>
            </select>
        </div>
        <h3>Student Lista:</h3>
        <ul v-if="!error" class="student-list">
            <li v-for="student in filteredStudents" :key="student.id">
                {{ student.studentName }} (Frånvaro: {{ student.absenceLength }})
            </li>
        </ul>
        <p v-else class="error-message">{{ error }}</p>
    </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, onMounted } from 'vue';
import axios from 'axios';

interface Student {
    studentId: number;
    studentName: string;
    schoolName: string;
    absenceLength: number;
}

export default defineComponent({
    setup() {
        const students = ref<Student[]>([]); 
        const selectedSchool = ref('');
        const error = ref('');

    onMounted(async () => {
    try {
            const response = await axios.get<Student[]>('https://localhost:7173/api/Student/students');
            students.value = response.data.map((item: Student) => ({
            studentId: item.studentId,
            studentName: item.studentName,
            schoolName: item.schoolName,
            absenceLength: item.absenceLength,
        }));
    } catch (e) {
        console.error('Error fetching students:', e);
        error.value = 'Det gick inte att läsa in elevdata. Försök igen senare.';
        students.value = [];
    }
    });


    const schoolNames = computed(() => {
        const schools = students.value.map(student => student.schoolName);
        return Array.from(new Set(schools));
    });

    const filteredStudents = computed(() => {
        if (!selectedSchool.value) return students.value;
        return students.value.filter(student => student.schoolName === selectedSchool.value);
    });

    return { 
        students, 
        selectedSchool, 
        schoolNames, 
        filteredStudents, 
        error 
    };
}});
</script>

<style scoped>
.student-tracker {
    font-family: 'Inter', sans-serif; /* Modern font */
    margin: 20px auto;
    padding: 24px;
    background-color: #f8f9fa; /* Softer background for better readability */
    border-radius: 12px; /* Larger radius for a more modern feel */
    box-shadow: 0 6px 12px rgba(0, 0, 0, 0.1); /* Slightly softer shadow */
    width: 100%;
    max-width: 800px; /* Set a maximum width for responsiveness */
    transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.student-tracker:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.15); /* Elevation effect on hover */
}

.title {
    font-size: 28px;
    font-weight: 600; /* Bold for emphasis */
    margin-bottom: 16px;
    color: #343a40; /* Neutral dark for readability */
}

.filter-container {
    margin-bottom: 24px;
}

.dropdown {
    padding: 12px 16px; /* Comfortable padding */
    font-size: 16px; /* Readable text size */
    font-weight: 500; /* Slightly bold text */
    border: 1px solid #ced4da; /* Subtle neutral border */
    border-radius: 12px; /* Large radius for a modern look */
    background-color: #ffffff; /* Clean white background */
    color: #495057; /* Neutral text color for readability */
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05); /* Light shadow for depth */
    width: 100%; /* Full width for responsiveness */
    max-width: 300px; /* Limit width */
    appearance: none; /* Remove native styling */
    cursor: pointer;
    transition: border-color 0.3s ease, box-shadow 0.3s ease;
}

.dropdown:focus {
    border-color: #007bff; /* Blue border for focus */
    box-shadow: 0 0 6px rgba(0, 123, 255, 0.3); /* Focus ring for accessibility */
    outline: none; /* Remove default focus outline */
}

.dropdown:hover {
    border-color: #0056b3; /* Slightly darker border on hover */
    box-shadow: 0 3px 6px rgba(0, 0, 0, 0.1); /* Subtle elevation effect */
}

.dropdown::after {
    content: '';
    position: absolute;
    top: 50%;
    right: 16px;
    width: 10px;
    height: 10px;
    border: solid #495057;
    border-width: 0 2px 2px 0;
    transform: translateY(-50%) rotate(45deg);
    pointer-events: none; /* Ensure arrow doesn't interfere with clicks */
}

.filter-container {
    position: relative; /* For positioning the dropdown arrow */
}

.filter-container .dropdown {
    padding-right: 40px; /* Space for the dropdown arrow */
}


.student-list {
    list-style-type: none;
    padding: 0;
    margin: 0;
}

.student-list li {
    background-color: #ffffff;
    margin-bottom: 12px;
    padding: 16px;
    border: 1px solid #dee2e6;
    border-radius: 10px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05); /* Subtle shadow */
    transition: transform 0.2s ease, box-shadow 0.2s ease;
    cursor: pointer; /* Interactive feel */
}

.student-list li:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); /* Slight elevation on hover */
}

.error-message {
    color: #dc3545; /* Modern red color for errors */
    font-size: 14px;
    margin-top: 12px;
    font-weight: 500;
}

</style>

