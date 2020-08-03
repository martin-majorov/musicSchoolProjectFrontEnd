<template>
  <div class="left-form">
            <button class="button-more-less" @click="changeShowSearch">
                <h2>Get Student Information</h2>
            </button>
            <transition name="fade">
                <div class="get-info left-form-input" v-if="this.windowProperties.showSearch">
                    <div class="inline-buttons">
                        <button class="button-top" v-on:click="getAllStudentsList">Get all students</button>
                        <button class="button-top" v-on:click="this.getAllPayments">Get all payments</button>
                        <button class="button-top" v-on:click="this.getAllLessons">Get all lessons</button>
                    </div>
                    <label for="first-name">First Name</label>
                    <input type="text" class="first-name" placeholder="Enter First Name..." v-model="student.firstName" />
                    <label for="last-name">Last Name</label>
                    <input type="text" class="last-name" placeholder="Enter Last Name..." v-model="student.lastName" />
                    <div class="inline-buttons">
                        <button class="button" v-on:click="this.getStudentInfo">Get Student Ballance</button>
                    </div>
                </div>
            </transition>
            <button class="button-more-less" v-on:click="changeShowAddPayment">
                <h2>Add New Payment</h2>
            </button>
            <transition name="fade">
                <div class="get-payment left-form-input" v-if="this.windowProperties.showAddPayment">
                    <label for="first-name">First Name</label>
                    <input type="text" class="first-name" placeholder="Enter First Name..." v-model="student.firstName" />
                    <label for="last-name">Last Name</label>
                    <input type="text" class="last-name" placeholder="Enter Last Name..." v-model="student.lastName" />
                    <div class="add-lesson-divider">
                        <div class="add-lesson-date">
                            <label for="date">Date</label>
                            <datepicker placeholder="Select Date" calendar-class="daypicker" v-model="date" @closed="$emit('chosenDate', date)"></datepicker>
                        </div>
                        <div class="add-lesson-payment">
                            <label for="amount">Payment</label>
                            <select class="input" name="amount" id="payment-amount" v-model="amount" v-on:change="$emit('chosenAmount', amount)">
                                <option value="15">15 Eur</option>
                                <option value="20">20 Eur</option>
                                <option value="25">25 Eur</option>
                                <option value="30">30 Eur</option>
                                <option value="35">35 Eur</option>
                                <option value="40">40 Eur</option>
                                <option value="45">45 Eur</option>
                                <option value="50">50 Eur</option>
                                <option value="55">55 Eur</option>
                                <option value="60">60 Eur</option>
                            </select>
                        </div>
                    </div>
                    <div class="inline-buttons">
                    <button class="button" v-on:click="createPayment">Add New Payment</button>
                    </div>
                </div>
            </transition>
            <button class="button-more-less" v-on:click="changeShowAddLesson">
                <h2>Add New Lesson</h2>
            </button>
            <transition name="fade">
                <div class="create-student left-form-input" v-if="this.windowProperties.showAddLesson">
                    <label for="first-name">First Name</label>
                    <input type="text" class="first-name" placeholder="Enter First Name..." v-model="student.firstName" />
                    <label for="last-name">Last Name</label>
                    <input type="text" class="last-name" placeholder="Enter Last Name..." v-model="student.lastName" />
                    <label for="amount">Lesson Date</label>
                    <datepicker placeholder="Select Date" calendar-class="daypicker" v-model="date" @closed="$emit('chosenDate', date)"></datepicker>
                    <div class="inline-buttons">
                        <button class="button" v-on:click="this.createLesson">Submit New Lesson</button>
                    </div>
                </div>
            </transition>
            <button class="button-more-less" v-on:click="changeShowAddStudent">
                <h2>Add New Student</h2>
            </button>
            <transition name="fade">
                <div class="create-student left-form-input" v-if="this.windowProperties.showAddStudent">
                    <label for="first-name">First Name</label>
                    <input type="text" class="first-name" placeholder="Enter First Name..." v-model="student.firstName" />
                    <label for="last-name">Last Name</label>
                    <input type="text" class="last-name" placeholder="Enter Last Name..." v-model="student.lastName" />
                    <label for="amount">Payment Rate</label>
                    <select class="input" name="rate" id="payment-amount" v-model="rate" v-on:change="$emit('chosenRate', rate)">
                        <option value="15">15 Eur Eur / Lesson </option>
                        <option value="20">20 Eur Eur / Lesson</option>
                    </select>
                    <div class="inline-buttons">
                        <button class="button" v-on:click="this.createStudent">Submit New Student</button>
                    </div>
                </div>
            </transition>
        </div>
</template>

<script>
import Datepicker from 'vuejs-datepicker';


export default {
  name: 'FormWindow',
  components: {
    Datepicker
  },
  props: [
    "windowProperties",
    "student",
    "studentsToView",
    "notFoundStudents",
    "currentStudentsList",
    "fullName",
    "createStudent",
    "paymentDate",
    "paymentAmount",
    "createPayment",
    "createLesson",
    "getAllPayments",
    "getAllLessons",
    "getStudentInfo"
  ],

  data() {
    return {
      currentStudentId: 0,
      date: '',
      rate: '',
      amount: ''
    }
  },

  methods: {
    
    changeShowSearch: function() {
      this.windowProperties.showSearch = !this.windowProperties.showSearch;
    },
    changeShowAddPayment: function() {
      this.windowProperties.showAddPayment = !this.windowProperties.showAddPayment;
    },

    changeShowAddStudent: function() {
      this.windowProperties.showAddStudent = !this.windowProperties.showAddStudent;
    },

    changeShowAddLesson: function() {
      this.windowProperties.showAddLesson = !this.windowProperties.showAddLesson;
    },

    // shows all students in the database by  array to the array to be viewed
        getAllStudentsList: async function () {
            const response = await fetch('http://localhost:3000/students')
                .then(res => res.json());

            if (this.currentStudentsList.length === 0) {
                response.students.forEach(student => {
                    this.currentStudentsList.push({
                        id: student.id,
                        name: student.name,
                        surname: student.surname
                    });
                })
            }
        }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.left-form {
    width: 50%;
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    background-color: #cb7f07;
    border-radius: 10px;
    padding: 20px 20px;
    margin: 0px 30px 0px 0px;
}


.button-top {
    font-family: 'Roboto', sans-serif;
    font-size: 1.03em;
    font-weight: 500;
    background-color:#5f787b;
    margin-top: 0;
    margin-bottom: 30px;
    width: 160px;
    height: 40px;
    border: 0;
    border-radius: 3px;
    outline: 0;
    color: white
}

@media only screen and (max-width: 700px) {
    .left-form {
        width: auto;
        display: flex;
        flex-direction: column;
        background-color:#cb7f07;
        border-radius: 10px;
        margin: 0px 0px 30px 0px;
    }

    .button-top {
        font-size: 0.9em;
        width: 120px;
    }
}

.left-form-input {
    display: flex;
    flex-direction: column;
    font-family: 'Roboto', sans-serif;
    color: white;
}


.button {
    font-family: 'Roboto', sans-serif;
    font-size: 1.03em;
    font-weight: 500;
    background-color: #5f787b;
    margin-top: 30px;
    margin-right: 20px;
    width: 180px;
    height: 40px;
    border: 0;
    border-radius: 3px;
    outline: 0;
    color: white
}

h2 {
    font-weight: 300;
}

.button-more-less {
    font-family: 'Roboto', sans-serif;
    font-size: 17px;
    background: none;
    text-align: left;
    color: white;
    border: 0;
    outline: 0;
}


.inline-buttons {
    display: flex;
    justify-content: space-between;
}

.button-more-less:hover {
    cursor: pointer;
}

.button:hover, .button-top:hover {
    cursor: pointer;
    background-color: #8ea6b2;
    color: white;
    border: 0;
    outline: 0;
}

label {
    padding: 7px 7px 7px 0px;
}

input, .input{
    font-family: 'Roboto', sans-serif;
    font-size: 0.9em;
    font-weight: 300;
    border: 0;
    border-radius: 3px;
    outline: 0;
    padding: 13px;
}

option {
    font-family: 'Roboto', sans-serif;
    font-size: 1.0em;
    padding: 5px;
}

.get-payment, .get-info, .create-student {
  width: 100%;
}

.daypicker header, .daypicker {
    color: black;
    background-color: white;
    display: block;
    margin: 0;
    padding: 0;
}

.fade-enter-active, .fade-leave-active {
    transition: opacity .5s;
  }
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
    opacity: 0;
  }

.add-lesson-divider {
    display: flex;
    justify-content: space-between;
}

.add-lesson-payment, .add-lesson-date {
    width: 100%;
}

.add-lesson-payment {
    display: flex;
    flex-direction: column;
}

.add-lesson-date {
    display: flex;
    flex-direction: column;
    margin-right: 30px;
}

</style>
