<template>
  <div class="right-form">
    <button class="button-more-less" v-on:click="this.clearResults">
        <h2>Search Results <span class="span-title">(press to clear)</span></h2> 
    </button>
    <WarningMessage v-if="this.warningMessage.switch" v-bind:warningMessage="this.warningMessage.text" />
    <SuccessfulMessage v-if="this.successfulMessage.switch" v-bind:successfulMessage="this.successfulMessage.text"/>
    <StudentNotFound v-bind:key="name" v-for="name in notFoundStudents" v-bind:name="name"></StudentNotFound>
    <GetFullName v-if="this.windowProperties.getFullName" />
    <div v-bind:key="student.id" v-for="student in this.currentStudentsList">
        <Student v-bind:student="student" 
                 @deleteStudent="$emit('deleteStudent', student.id)"
                 @studentInfo="$emit('studentInfo', student)"
                 @editStudent="sendEditedName"/>
    </div>
    <div v-bind:key="payment.id" v-for="payment in this.allPayments">
        <Payment    v-bind:payment="payment"
                    @deletePayment="$emit('deletePayment', payment.id)"
                    @updatePayment="updatePayment"
         />
    </div>
    <div v-bind:key="lesson.id" v-for="lesson in this.allLessons">
        <Lesson v-bind:lesson="lesson" 
                    @deleteLesson="$emit('deleteLesson', lesson.id)"
                    @updateLesson="updateLesson"
        />
    </div>
    <StudentBallance
                v-bind:key="student.id"
                v-for="student in this.studentsToView" v-bind:name="student.name"
                v-bind:payments="student.totalPayments" v-bind:lessons="student.totalLessons"
                v-bind:paymentballance="student.lessonsLeft" 
    />
    <NewStudent v-bind:key="student.id"
                v-for="student in this.newStudent"
                v-bind:name="student.name"
                v-bind:surname="student.surname"
                v-bind:paymentRate="student.payment_rate"
    />
    <NewPayment v-bind:key="payment.id" 
                v-for="payment in this.newPayment"
                v-bind:name="payment.name"
                v-bind:date="payment.date"
                v-bind:paymentAmount="payment.paymentAmount"
    />
    <NewLesson  v-bind:key="lesson.id" 
                v-for="lesson in this.newLesson" 
                v-bind:name="lesson.name" 
                v-bind:date="lesson.date"
                v-bind:length="lesson.length"
    />
  </div>
</template>

<script>
import StudentNotFound from './StudentNotFound'
import StudentBallance from './StudentBallance'
import Student from './Student'
import Payment from './Payment'
import Lesson from './Lesson'
import GetFullName from './GetFullName'
import NewStudent from './NewStudent'
import NewPayment from './NewPayment'
import NewLesson from './NewLesson'
import WarningMessage from './WarningMessage'
import SuccessfulMessage from './SuccessfulMessage'

export default {
    name: 'ResultWindow',
    components: {
        StudentNotFound,
        StudentBallance,
        GetFullName,
        NewStudent,
        NewPayment,
        WarningMessage,
        SuccessfulMessage,
        NewLesson,
        Student,
        Lesson,
        Payment
    },
    props: [
        'windowProperties',
        'notFoundStudents',
        'paymentBallance',
        'studentsToView',
        'currentStudentsList',
        'newStudent',
        'newPayment',
        'newLesson',
        'warningMessage',
        'successfulMessage',
        'clearResults',
        'allLessons',
        'allPayments'
    ],
    methods: {
        sendEditedName: function(newNameObj) {
            this.$emit('editStudent', newNameObj);
        },
        updatePayment: function(payment) {
            this.$emit('updatePayment', payment);
        },
        updateLesson: function(lesson) {
            this.$emit('updateLesson', lesson);
        }
    }
}
</script>

<style>
.right-form {
    width:50%;
    font-family: 'Roboto', sans-serif;
    color: white;
    background-color: #cb7f07;
    border-radius: 10px;
    padding: 20px 20px;
}

@media only screen and (max-width: 700px) {
      .right-form {
        width: auto;
        font-family: 'Roboto', sans-serif;
        color: white;
        background-color: #cb7f07;
        border-radius: 10px;

    }
}

.student-info-form {
    padding: 10px;
    border-color: white;
    border: 1px;
    border-radius: 5px;
    margin: 30px;
}

.student-info-form div {
    padding: 15px;
}

.student-form-names {
    text-align: center;
    padding: 20px;
    font-size: 1.1em;
    font-weight: bold;
}

.student-form-numbers {
    padding: 20px;
    font-size: 1.2em;
    font-weight: bold;
}

.table-right {
    text-align: right;
}

.table-left {
    text-align: left;
}

.fade-enter-active, .fade-leave-active {
    transition: opacity .5s;
  }
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
    opacity: 0;
  }

.student-not-found {
    padding: 10px;
    background-color: orangered;
    border-color: white;
    border: 1px;
    border-radius: 5px;
    border-style: dashed;
    margin: 30px;
}

.span-title {
    font-size: 0.5em;
    font-weight: normal;
}
</style>