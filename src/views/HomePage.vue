<template>
    <main>

        <h1 class="title">Tìm kiếm</h1>
        <div class="search-bar">
            <input type="text" id="studentID" @keyup.enter="searchStudent" onkeyup="this.value = this.value.toUpperCase();"
                v-model="studentId" placeholder="Nhập mã số sinh viên...">
        </div>

        <button id="searchButton" @click="searchStudent">
            Tìm kiếm
        </button>

        <div class="loading-overlay" id="loadingOverlay">
            <div class="loading-icon"></div>
        </div>

        <h1 v-if="studentId !== '' && !isNotFound()" class="title">Kết quả tìm kiếm</h1>

        <div v-if="studentId !== '' && !isNotFound()" class="studentInfo">
            <div class="student-details">
                <p id="studentName">Họ tên: {{ student.name }}</p>
                <p id="studentMSSV">MSSV: {{ student.mssv }}</p>
                <p id="studentNganhKhoa">Ngành học - Khóa: {{ student.nganhkhoa }}</p>
            </div>
            <div class="student-activities">
                <!-- Các hoạt động sinh viên đã tham gia sẽ được hiển thị ở đây -->
                <table class="activity-table">
                    <thead>
                        <tr>
                            <th>STT</th>
                            <th>Hoạt động</th>
                            <th>Điểm</th>
                            <th>Tổng điểm</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(activity, activityIdx) in  student.activities" :key="activity.name">
                            <td>{{ activityIdx + 1 }}</td>
                            <td>{{ activity.name }}</td>
                            <td>{{ activity.diem }}</td>
                            <td v-if="activityIdx == 0" :rowSpan="student.activities.length">{{ totalScore() }}</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
        <p v-else-if="studentId !== '' && isNotFound()">Bạn chưa tham gia hoạt động nào trong học kỳ
            này</p>
    </main>
</template>

<script>

import { db } from '@/firebase';
import { collection, setDoc, addDoc, query, where, onSnapshot, updateDoc, doc, deleteDoc, orderBy } from "firebase/firestore";

export default {
    name: 'HomePage',
    data() {
        return {
            studentId: '',
            students: [],
            student: {},
        };
    },
    methods: {

        async searchStudent() {
            document.getElementById("loadingOverlay").style.visibility = "visible";
            onSnapshot(doc(db, "students", this.studentId), (doc) => {
                this.student = doc.data();
            });
            document.getElementById("loadingOverlay").style.visibility = "hidden";
        },
        isNotFound() {
            let value = this.student;

            return value === undefined || Object.keys(value).length === 0

            // return value // 👈 null and undefined check
            //     && Object.keys(value).length === 0 && value.constructor === Object;
        },
        totalScore() {
            let score = 0;
            this.student.activities.forEach(activity => {
                score += parseInt(activity.diem);
            });
            score = parseInt(score);
            score = (score > 10) ? 10 : score;
            return score;
        },
    },

    async created() {
        // const colRef = collection(db, "students");
        // onSnapshot(colRef, (snapshot) => {
        //     let res = [];
        //     snapshot.docs.forEach((doc) => {
        //         res.push({ ...doc.data(), id: doc.id });
        //     })
        //     this.students = res;
        // })
    }
}
</script>

<style></style>