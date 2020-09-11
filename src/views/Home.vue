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
        v-on:updatePayment="updatePayment"
        v-on:updateLesson="updateLesson"
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

    getStudentIdFromDatabase: async function(student) {
      let name = student[0];
      let surname = student[1];

      const response = await fetch(`https://mspdeployment.ew.r.appspot.com/name?name=${name}&surname=${surname}`)
                .then(res => res.json())
                .then(json => {return json.student._id});

      return response;
    },

    callStudentInfo: async function(student) {
        this.student.firstName = student.name;
        this.student.lastName = student.surname;
        this.getStudentInfo();
    },

    updatePayment: async function(payment) {
      const newPaymentAmount = payment.editedAmount;
      const newPaymentDate = payment.editedDate;
      const paymentId = payment.id;

      // validates if amount consists of numbers
      let validateAmount = String(newPaymentAmount).match(/\D/gi);
      // validates if name has no special characters
      let validateDate = newPaymentDate.match(/[$-,:.-/:-?{-~!"^_`\s+]/gi);
      // validates if date is in apropriate format YYYY-MM-DD
      let validateDateFormat = newPaymentDate.match(/\d{4}-\d{2}-\d{2}/);

      if(!newPaymentAmount || !newPaymentDate || !paymentId || validateAmount || validateDate) {
        this.warningMessage.text = "Cannot use special character (!@#$%^&*()_/*-) for name or date";
        this.warningMessage.switch = true;
      } else if(!validateDateFormat) {
        this.warningMessage.text = "Please use following format for the date: YYYY-MM-DD";
        this.warningMessage.switch = true;
      } else {

        // if validation is passed, updates local payment object
        this.allPayments.forEach(payment => {
          if(payment.id === paymentId) {
            payment.date = newPaymentDate.trim();
            payment.payment_amount = Number(newPaymentAmount);
          }
        })

        // filters the name and surname of the lesson attender. It is nescessary because in backend,
        // we have student.id parameter to update the lesson data and there is no other way than retrieve it from DB by using name and surname.
        const nameSurnameArray = this.allPayments.filter(payment => payment.id === paymentId)[0].name.split(" ");

        //uses the async function "getStudentIdFromDatabase" to get the ID. Function needs a [name, surname] in the input.
        // function is async, because of the request to DB, so it needs to have await before using the value.
        const thisPaymentsStudentId = this.getStudentIdFromDatabase(nameSurnameArray);
        
        // if validation is passed, updates the database payment record
        // data object for update request
        const data = {
          payment: {
            student_id: await thisPaymentsStudentId,
            date: newPaymentDate,
            payment_amount: Number(newPaymentAmount)
          }
        }
        //update url
        const url = `https://mspdeployment.ew.r.appspot.com/payments/${paymentId}`;


        //update method
        const method = {
          method: 'PUT',
          headers: {'Content-Type': 'application/json'},
          body: JSON.stringify(data)
        }

        fetch(url, method);

      }
    },

    updateLesson: async function(lesson) {
      const newLessonLength = lesson.lessonLength;
      const newLessonDate = lesson.lessonDate;
      const lessonId = lesson.id;

      // validates if lesson length is a number
      let validateLength = String(newLessonLength).match(/\D/gi);
      // validates if name has no special characters
      let validateDate = newLessonDate.match(/[$-,:.-/:-?{-~!"^_`\s+]/gi);
      // validates if date is in apropriate format YYYY-MM-DD
      let validateDateFormat = newLessonDate.match(/\d{4}-\d{2}-\d{2}/);

      if(!newLessonLength || !newLessonDate || !lessonId || validateLength || validateDate) {
        this.warningMessage.text = "Cannot use special character (!@#$%^&*()_/*-) for lesson length or date";
        this.warningMessage.switch = true;
      } else if(!validateDateFormat) {
        this.warningMessage.text = "Please use following format for the date: YYYY-MM-DD";
        this.warningMessage.switch = true;
      } else {

        // if validation is passed, updates local payment object
        this.allLessons.forEach(lesson => {
          if(lesson.id === lessonId) {
            lesson.date = newLessonDate.trim();
            lesson.lesson_length = Number(newLessonLength);
          }
        })

        // filters the name and surname of the lesson attender. It is nescessary because in backend,
        // we have student.id parameter to update the lesson data and there is no other way than retrieve it from DB by using name and surname.
        const nameSurnameArray = this.allLessons.filter(lesson => lesson.id === lessonId)[0].name.split(" ");

        //uses the async function "getStudentIdFromDatabase" to get the ID. Function needs a [name, surname] in the input.
        // function is async, because of the request to DB, so it needs to have await before using the value.
        const thisLessonStudentId = this.getStudentIdFromDatabase(nameSurnameArray);
        
        // data object for update request
        const data = {
          lesson: {
            student_id: await thisLessonStudentId,
            date: newLessonDate,
            lesson_length: newLessonLength
          }
        }

        console.log(data);
        //update url
        const url = `https://mspdeployment.ew.r.appspot.com/lessons/${lessonId}`;


        //update method
        const method = {
          method: 'PUT',
          headers: {'Content-Type': 'application/json'},
          body: JSON.stringify(data)
        }

        fetch(url, method);

        
      }
    },

    editStudent: async function(newNameObj){

      // splits the incoming object with full name string and ID to separate name surname array.
      const newName = newNameObj.newName.split(" ");

      //validates if name has no special characters 
      let validateName = newName[0].match(/[0-9$-/:-?{-~!"^_`\s+]/gi);
      let validateSurname = newName[1].match(/[0-9$-/:-?{-~!"^_`\s+]/gi);

      //triggers an error string if name or surname has special characters or name has no name or surname
      if(validateName || validateSurname || newName.length != 2) {
        this.warningMessage.text = "Cannot use special character (!@#$%^&*()_/*-) or numbers for name! Full name must consist of two words."
        this.warningMessage.switch = true;
      } else {

        let currentPaymentRate;

        //updates local name object
        this.currentStudentsList.forEach(student => {
          if(student.id === newNameObj.id) {
            student.name = newName[0].trim();
            student.surname = newName[1].trim();
            currentPaymentRate = student.paymentRate;
          }
        })

        //updates database
        const data = {
          student: {
            name: newName[0],
            surname: newName[1],
            payment_rate: currentPaymentRate
          }
        }

        console.log(data);
        console.log(newNameObj.id);
        
        const url = `https://mspdeployment.ew.r.appspot.com/students/${newNameObj.id}`;

        const method = {
          method: 'PUT',
          headers: {'Content-Type': 'application/json'},
          body: JSON.stringify(data)
        }

        fetch(url, method).then(res => res.json());

      }
      
    },

    deleteStudent: async function(id) {
      const url = `https://mspdeployment.ew.r.appspot.com/students/${id}`;
      const method = {
          method: 'DELETE',
        };

      await fetch(url, method);

      this.currentStudentsList = this.currentStudentsList.filter(student => student.id != id);

    },

    deletePayment: async function(id) {
      const url = `https://mspdeployment.ew.r.appspot.com/payments/${id}`;
      const method = {
          method: 'DELETE',
        };

      await fetch(url, method);

      this.allPayments = this.allPayments.filter(payment => payment.id != id);
    },
    
    deleteLesson: async function(id) {
      const url = `https://mspdeployment.ew.r.appspot.com/lessons/${id}`;
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
      return await fetch(`https://mspdeployment.ew.r.appspot.com/name?name=${name}&surname=${surname}`)
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
            student_id: response.student._id,
            date: this.customDateFormat,
            payment_amount: this.paymentAmount
          }
        };

        const url = 'https://mspdeployment.ew.r.appspot.com/payments';

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
          id: confirm._id,
          name: this.fullName,
          date: confirm.date,
          paymentAmount: confirm.paymentAmount
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
        const url = 'https://mspdeployment.ew.r.appspot.com/students';
        const method = {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(data)
        };

        const response = await fetch(url, method)
          .then(res => res.json());
        this.newStudent.push(response);
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
            student_id: response.student._id,
            date: this.customDateFormat,
            lesson_length: 45
          }
        };

        const url = 'https://mspdeployment.ew.r.appspot.com/lessons';

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
          id: confirm._id,
          name: this.fullName,
          date: confirm.date,
          length: confirm.lessonLength
        });

        this.windowProperties.newLesson = true;

      }
    },

    getAllLessons: async function() {
      if(this.allLessons.length === 0) {
        const allStudents = await fetch('https://mspdeployment.ew.r.appspot.com/students').then(res => res.json());

        const url = 'https://mspdeployment.ew.r.appspot.com/lessons';
        const allLessons = await fetch(url).then(res => res.json());


        allLessons.lessons.forEach(lesson => this.allLessons.push({
            id: lesson._id, // changed to be compatible with MongoDB
            name: `${allStudents.students.filter(student => student._id === lesson.studentId)[0].name} ${allStudents.students.filter(student => student._id === lesson.studentId)[0].surname}`,
            date: lesson.date,
            lesson_length: lesson.lessonLength
          }));
      }
        
    },

    getAllPayments: async function() {
        if(this.allPayments.length === 0) {
          const allStudents = await fetch('https://mspdeployment.ew.r.appspot.com/students').then(res => res.json());

          const url = 'https://mspdeployment.ew.r.appspot.com/payments';
          const allPayments = await fetch(url).then(res => res.json());
        
          allPayments.payments.forEach(payment => this.allPayments.push({
            id: payment._id,
            name: `${allStudents.students.filter(student => student._id === payment.studentId)[0].name} ${allStudents.students.filter(student => student._id === payment.studentId)[0].surname}`,
            date: payment.date,
            payment_amount: payment.paymentAmount
          }));
        }
    },

    getStudentId: async function () {
      const name = this.student.firstName;
      const surname = this.student.lastName;
      if (!name || !surname) {
           return this.windowProperties.getFullName = true;
      }

      const response = await fetch(`https://mspdeployment.ew.r.appspot.com/name?name=${name}&surname=${surname}`)
                .then(res => res.json());
            
      if (!response.student) {
          this.notFoundStudents.push(this.fullName);
      } else {
          return {
            currentStudentId: response.student._id,
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
          const totalLessons = await fetch(`https://mspdeployment.ew.r.appspot.com/students/${idWithPaymentRate.currentStudentId}/total-lessons`)
                    .then(res => res.json())
                    .catch(err => console.log(err));

          const totalPayments = await fetch(`https://mspdeployment.ew.r.appspot.com/students/${idWithPaymentRate.currentStudentId}/total-payments`)
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