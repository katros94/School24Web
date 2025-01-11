<template>
    <div class="random-absences">
        <h2>Frånvaroanmälningar</h2>
        <button @click="getRandomStudents" class="random-button">+</button>
        <h2>Frånvaroanmälningar</h2>
        <ul>
            <li v-for="student in randomStudents" :key="student.id">
                <p><strong>Name:</strong> {{ student.studentName }}</p>
                <p><strong>Absence Length:</strong> {{ student.absenceLength }}</p>
            </li>
        </ul>
        <p v-if="randomStudents.length === 0">Inga elever har valts, prova att klicka på + knappen ovan.</p>
    </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';
import axios from 'axios';

export default defineComponent({
    setup() {
        const students = ref([]);
        const randomStudents = ref([]);

        onMounted(async () => {
    try {
        const response = await axios.get('https://localhost:7173/api/Student/students');
        students.value = response.data;
    } catch (e) {
        console.error('Error fetching students:', e);
        students.value = [];
        alert('Failed to load students. Please try again later.');
    }
});

const getRandomStudents = () => {
    const numberOfRandomStudents = 5;
    if (students.value.length > 0) {
        const selectedIndexes = new Set<number>();
        while (selectedIndexes.size < numberOfRandomStudents) {
            const randomIndex = Math.floor(Math.random() * students.value.length);
            selectedIndexes.add(randomIndex);
        }
        randomStudents.value = Array.from(selectedIndexes).map(index => students.value[index]);
    }
};

return { randomStudents, getRandomStudents };
}
});
</script>

<style scoped>
.random-absences {
    font-family: 'Arial, sans-serif';
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: top;
    height: 100vh;
}

.random-button {
    font-size: 80px;
    cursor: pointer;
    background-color: #fff;
    color: #000;
    width: 998px;
    height: 200px;
    border: none;
    border-radius: 4px;
    transition: background-color 0.3s ease;
    border-radius: 8px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.random-button:hover {
    color: #0056b3;
}

ul {
    list-style-type: none;
    padding: 0;
}

li {
    background-color: #fff;
    color: #000;
    width: 300px;
    height: 100px;
    border: none;
    border-radius: 4px;
    transition: background-color 0.3s ease;
    border-radius: 8px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    margin-bottom: 50px;
}

p {
    margin: 8px 0;
}

p strong {
    font-weight: bold;
}
</style>
