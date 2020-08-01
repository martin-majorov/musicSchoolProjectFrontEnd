<template>
  <div class="main-container">
    <FormWindow 
        v-bind:windowProperties="windowProperties"
        v-bind:student="student"
        v-bind:studentsToView="studentsToView"
        v-bind:notFoundStudents="notFoundStudents"
        v-bind:currentStudentsList="currentStudentsList"
        v-bind:fullName="fullName"
        v-bind:createStudent="createStudent"
        v-bind:paymentDate="paymentDate"
        v-bind:paymentAmount="paymentAmount"
        v-bind:createPayment="createPayment"
        v-bind:createLesson="createLesson"
        v-bind:getAllLessons="getAllLessons"
        v-bind:getAllPayments="getAllPayments"
        v-bind:getStudentInfo="getStudentInfo"
        v-on:chosenDate="setPaymentDate"
        v-on:chosenAmount="setPaymentAmount"
        v-on:chosenRate="setPaymentRate"
         />
    <ResultWindow 
        v-bind:clearResults="clearResults"
        v-bind:notFoundStudents="notFoundStudents"
        v-bind:studentsToView="studentsToView"
        v-bind:currentStudentsList="currentStudentsList"
        v-bind:windowProperties="windowProperties"
        v-bind:newStudent="newStudent"
        v-bind:newPayment="newPayment"
        v-bind:newLesson="newLesson"
        v-bind:warningMessage="warningMessage"
        v-bind:successfulMessage="successfulMessage"
        v-bind:allLessons="allLessons"
        v-bind:allPayments="allPayments"
        v-on:editStudent="editStudent"
        v-on:deleteStudent="deleteStudent"
        v-on:deletePayment="deletePayment"
        v-on:deleteLesson="deleteLesson"
        v-on:studentInfo="callStudentInfo"
    />
  </div>
</template>

<script>
// @ is an alias to /src
import FormWindow from '@/components/FormWindow.vue'
import ResultWindow from '@/components/ResultWindow.vue'
import moment from 'moment';

export default {
  name: 'Home',
  components: {
    FormWindow,
    ResultWindow
  },
  data() {
    return {
        student: {
          firstName: '',
          lastName: ''
        },
        studentsToView: [],
        notFoundStudents: [],
        currentStudentsList: [],
        newStudent: [],
        newPayment: [],
        newLesson: [],
        allLessons: [],
        allPayments: [],
        warningMessage: {
          switch: false,
          text: ''
        },
        successfulMessage: {
          switch: false,
          text: ''
        },
      windowProperties: {
        showAddLesson: false,
        showSearch: false,
        showAddPayment: false,
        showAddStudent: false,
        showStudentSearch: false,
        getFullName: false
      },
      paymentDate: '',
      paymentAmount: '',
      paymentRate: ''
    }
  },

  computed: {
        
        fullName: function () {
            return this.student.firstName.concat(' ').concat(this.student.lastName)
        },

        customDateFormat: function() {
            return moment(this.paymentDate).format('YYYY-MM-DD');
        }
  },

  methods: {

    callStudentInfo: async function(student) {
        this.student.firstName = student.name;
        this.student.lastName = student.surname;
        this.getStudentInfo();
    },

    editStudent: async function(newNameObj){

      // splits the incoming object with full name string and ID to separate name surname array.
      const newName = newNameObj.newName.split(" ");

      //validates if name has no special characters 
      let validateName = newName[0].match(/[0-9$-/:-?{-~!"^_`\s+]/gi);
      let validateSurname = newName[1].match(/[0-9$-/:-?{-~!"^_`\s+]/gi);

      //triggers an error string if name or surname has special characters or name has no name or surname
      if(validateName || validateSurname || newName.length != 2) {
        this.warningMessage.text = "You cannot use special character (!@#$%^&*()_/*-) or numbers for name! Full name must consist of two words."
        this.warningMessage.switch = true;
      } else {

        //updates local name object
        this.currentStudentsList.forEach(student => {
          if(student.id === newNameObj.id) {
            student.name = newName[0].trim();
            student.surname = newName[1].trim();
          }
        })

        //updates database
        const data = {
          student: {
            name: newName[0],
            surname: newName[1]
          }
        }
        
        const url = `http://localhost:3000/students/${newNameObj.id}/editname`;

        const method = {
          method: 'PUT',
          headers: {'Content-Type': 'application/json'},
          body: JSON.stringify(data)
        }

        fetch(url, method).then(res => res.json());

      }
      
    },

    deleteStudent: async function(id) {
      const url = `http://localhost:3000/students/${id}`;
      const method = {
          method: 'DELETE',
        };

      await fetch(url, method);

      this.currentStudentsList = this.currentStudentsList.filter(student => student.id != id);

    },

    deletePayment: async function(id) {
      const url = `http://localhost:3000/payments/${id}`;
      const method = {
          method: 'DELETE',
        };

      await fetch(url, method);

      this.allPayments = this.allPayments.filter(payment => payment.id != id);
    },
    
    deleteLesson: async function(id) {
      const url = `http://localhost:3000/lessons/${id}`;
      const method = {
          method: 'DELETE',
        };

      await fetch(url, method);

      this.allLessons = this.allLessons.filter(lesson => lesson.id != id);
    },

    setPaymentDate: function(date) {
      return this.paymentDate = date;
    },

    setPaymentAmount: function(amount) {
      return this.paymentAmount = amount;
    },

    setPaymentRate: function(rate) {
      return this.paymentRate = rate;
    },

    clearResults: function () {
            this.studentsToView = [];
            this.notFoundStudents = [];
            this.student.firstName = '';
            this.student.lastName = '';
            this.currentStudentsList = [];
            this.windowProperties.showStudentSearch = false;
            this.windowProperties.getFullName = false;
            this.newStudent = [];
            this.newPayment = [];
            this.newLesson = [];
            this.allLessons = [];
            this.allPayments = [];
            this.warningMessage = {
                switch: false,
                text: ''
                };
    },

    checkDublicateStudent: async function () {
      const name = this.student.firstName;
      const surname = this.student.lastName;
      return await fetch(`http://localhost:3000/name?name=${name}&surname=${surname}`)
                .then(res => res.json());
    },

    createPayment: async function ()   {
      const name = this.student.firstName;
      const surname = this.student.lastName;
      const paymentDate = this.paymentDate;
      const paymentAmount = this.paymentAmount;

      if(!name || !surname || !paymentAmount || !paymentDate) {
        return this.warningMessage = {
          switch: true,
          text: "Missing data (name or surname or date or payment)"
        }
      } else {

        const response = await this.checkDublicateStudent();

        if(!response.student) {
            return this.warningMessage = {
              switch: true,
              text: "There is no such student!"
            }
        }

        const data = {
          payment: {
            student_id: response.student.id,
            date: this.customDateFormat,
            payment_amount: this.paymentAmount
          }
        };

        const url = 'http://localhost:3000/payments';

        const method = {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(data)
        };

        const confirm = await fetch(url, method)
          .then(res => res.json());

        this.newPayment.push({
          id: confirm.payment.id,
          name: this.fullName,
          date: confirm.payment.date,
          paymentAmount: confirm.payment.payment_amount
        });
        this.windowProperties.newPayment = true;

      }
    },
    
    createStudent: async function () {
      const studentExists = await this.checkDublicateStudent();
      if(studentExists.student) {
        return this.warningMessage = {
          switch: true,
          text: "The student already exists!"
        }
      } else if (this.student.firstName && this.student.lastName && this.paymentRate) {
          const data = {
          student: {
              name: this.student.firstName,
              surname: this.student.lastName,
              payment_rate: this.paymentRate
          }
        };
        const url = 'http://localhost:3000/students';
        const method = {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(data)
        };

        const response = await fetch(url, method)
          .then(res => res.json());
        this.newStudent.push(response.student);
        this.windowProperties.newStudent = true;

        } else {
          return this.windowProperties.getFullName = true;
        }
    },

    createLesson: async function() {
      const name = this.student.firstName;
      const surname = this.student.lastName;
      const paymentDate = this.paymentDate;

      if(!name || !surname || !paymentDate) {
        return this.warningMessage = {
          switch: true,
          text: "Missing data (name or surname or date or payment)"
        }
      } else {

        const response = await this.checkDublicateStudent();

        if(!response.student) {
            return this.warningMessage = {
              switch: true,
              text: "There is no such student!"
            }
        }

        const data = {
          lesson: {
            student_id: response.student.id,
            date: this.customDateFormat,
            lesson_length: 45
          }
        };

        const url = 'http://localhost:3000/lessons';

        const method = {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(data)
        };

        const confirm = await fetch(url, method)
          .then(res => res.json());

        this.newLesson.push({
          id: confirm.lesson.id,
          name: this.fullName,
          date: confirm.lesson.date,
          length: confirm.lesson.lesson_length
        });

        this.windowProperties.newLesson = true;

      }
    },

    getAllLessons: async function() {
      if(this.allLessons.length === 0) {
        const allStudents = await fetch('http://localhost:3000/students').then(res => res.json());

        const url = 'http://localhost:3000/lessons';
        const allLessons = await fetch(url).then(res => res.json());

        allLessons.lessons.forEach(lesson => this.allLessons.push({
            id: lesson.id,
            name: `${allStudents.students.filter(student => student.id == lesson.student_id)[0].name} ${allStudents.students.filter(student => student.id == lesson.student_id)[0].surname}`,
            date: lesson.date,
            lesson_length: lesson.lesson_length
          }));
      }
        
    },

    getAllPayments: async function() {
        if(this.allPayments.length === 0) {
          const allStudents = await fetch('http://localhost:3000/students').then(res => res.json());

          const url = 'http://localhost:3000/payments';
          const allPayments = await fetch(url).then(res => res.json());
        
          allPayments.payments.forEach(payment => this.allPayments.push({
            id: payment.id,
            name: `${allStudents.students.filter(student => student.id == payment.student_id)[0].name} ${allStudents.students.filter(student => student.id == payment.student_id)[0].surname}`,
            date: payment.date,
            payment_amount: payment.payment_amount
          }));
        }
        
    },

    getStudentId: async function () {
      const name = this.student.firstName;
      const surname = this.student.lastName;
      if (!name || !surname) {
           return this.windowProperties.getFullName = true;
      }

      const response = await fetch(`http://localhost:3000/name?name=${name}&surname=${surname}`)
                .then(res => res.json());
            
      if (!response.student) {
          this.notFoundStudents.push(this.fullName);
      } else {
          return {
            currentStudentId: response.student.id,
            currentStudentRate: response.student.payment_rate
          };
      }
    },

    getStudentInfo: async function () {
      const idWithPaymentRate = await this.getStudentId();
      this.currentStudentId = idWithPaymentRate.currentStudentId;
            
      if (this.currentStudentId === 0) {
          return this.notFoundStudents.push(this.fullName);
      } else if (this.currentStudentId) {
          const totalLessons = await fetch(`http://localhost:3000/students/${idWithPaymentRate.currentStudentId}/total-lessons`)
                    .then(res => res.json())
                    .catch(err => console.log(err));

          const totalPayments = await fetch(`http://localhost:3000/students/${idWithPaymentRate.currentStudentId}/total-payments`)
                    .then(res => res.json())
                    .catch(err => console.log(err));

          const totalPaymentsNumber = Number(totalPayments.totalPayments.total_payments);
          const totalLessonsNumber = Number(totalLessons.totalLessons.total_lessons);
          const currentStudentRate = Number(idWithPaymentRate.currentStudentRate);

          const lessonsLeft = (totalPaymentsNumber / currentStudentRate) - totalLessonsNumber;

          return this.studentsToView.push({
              currentStudentId: idWithPaymentRate.currentStudentId,
              name: this.fullName,
              totalPayments: totalPaymentsNumber,
              totalLessons: totalLessonsNumber,
              currentStudentRate: currentStudentRate,
              lessonsLeft: lessonsLeft
            });
          }
    }
  }
}
</script>

<style>
  .main-container {
    display: flex;
    align-items: stretch;        
    justify-content: space-between;
    margin: 30px;
    text-align: left;
    }

  @media only screen and (max-width: 700px) {
    .main-container {
        display: flex;
        flex-direction: column;
        align-items: stretch;
        margin: 30px;
        text-align: left;
    }
  }
</style>