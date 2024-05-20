<template>
  <div class="registration-form">
    <h1>Registration Form</h1>
    <form @submit.prevent="submitForm">
      <div>
        <label for="fullName">Full Name as per IC:</label>
        <input type="text" v-model="form.fullName" id="fullName" required>
      </div>
      <div>
        <label for="icNumber">IC Number:</label>
        <input type="text" v-model="form.icNumber" id="icNumber" @input="validateAndParseIC" required>
        <span v-if="icError" class="error">{{ icError }}</span>
      </div>
      <div>
        <label for="gender">Gender:</label>
        <select v-model="form.gender" id="gender" required>
          <option value="">Select Gender</option>
          <option value="male">Male</option>
          <option value="female">Female</option>
        </select>
      </div>
      <div>
        <label for="dob">Date of Birth:</label>
        <input type="date" v-model="form.dob" id="dob" required>
      </div>
      <div>
        <label for="nationality">Nationality:</label>
        <input type="text" v-model="form.nationality" id="nationality" required>
      </div>
      <div>
        <label for="email">Email Address:</label>
        <input type="text" v-model="form.emailLocalPart" id="emailLocalPart" required @input="updateEmail">
        @
        <select v-model="form.emailDomain" @change="updateEmail" required>
          <option value="gmail.com">gmail.com</option>
          <option value="yahoo.com">yahoo.com</option>
          <option value="yahoo.com.my">yahoo.com.my</option>
          <option value="outlook.com">outlook.com</option>
          <option value="hotmail.com">hotmail.com</option>
          <option value="live.com">live.com</option>
          <option value="icloud.com">icloud.com</option>
          <option value="custom">Custom</option>
        </select>
        <input type="text" v-if="form.emailDomain === 'custom'" v-model="form.customEmailDomain" @input="updateEmail">
      </div>
      <button type="submit">Register</button>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      form: {
        fullName: '',
        icNumber: '',
        gender: '',
        dob: '',
        nationality: '',
        emailLocalPart: '',
        emailDomain: 'gmail.com',
        customEmailDomain: '',
        email: ''
      },
      icError: ''
    }
  },
  methods: {
    submitForm() {
      console.log('Form data:', this.form);
      // Handle form submission logic here, e.g., sending data to a server
    },
    validateAndParseIC() {
      const icNumber = this.form.icNumber;
      const icPattern = /^\d{12}$/;
      const nonNumericPattern = /^[a-zA-Z]/;

      if (nonNumericPattern.test(icNumber)) {
        // Assume it's a passport number
        this.icError = '';
        return;
      }

      if (icNumber.length >= 8 && icNumber.length <= 14) {
        if (!icPattern.test(icNumber)) {
          this.icError = 'For Malaysia, IC number must be 12 digits';
          return;
        }

        const year = icNumber.slice(0, 2);
        const month = icNumber.slice(2, 4);
        const day = icNumber.slice(4, 6);
        const genderDigit = icNumber.slice(11, 12);

        if (!this.isValidDate(day, month, year)) {
          this.icError = 'Invalid date of birth';
          return;
        }

        const century = parseInt(year) <= 30 ? '20' : '19';
        const dob = `${century}${year}-${month}-${day}`;
        const gender = genderDigit % 2 === 0 ? 'female' : 'male';

        this.form.gender = gender;
        this.form.dob = dob;
        this.form.nationality = 'Malaysian'; // Assuming all valid IC numbers are Malaysian

        this.icError = '';
      } else {
        this.icError = '';
      }
    },
    isValidDate(day, month, year) {
      const century = parseInt(year) <= 30 ? '20' : '19';
      const date = new Date(`${century}${year}-${month}-${day}`);
      return date && date.getFullYear() === parseInt(`${century}${year}`) &&
        date.getMonth() + 1 === parseInt(month) &&
        date.getDate() === parseInt(day);
    },
    updateEmail() {
      if (this.form.emailDomain === 'custom') {
        this.form.email = `${this.form.emailLocalPart}@${this.form.customEmailDomain}`;
      } else {
        this.form.email = `${this.form.emailLocalPart}@${this.form.emailDomain}`;
      }
    }
  }
}
</script>

<style scoped>
.registration-form {
  max-width: 600px;
  margin: 0 auto;
}
.registration-form h1 {
  text-align: center;
}
.registration-form form {
  display: flex;
  flex-direction: column;
}
.registration-form div {
  margin-bottom: 1rem;
}
.registration-form label {
  margin-bottom: 0.5rem;
}
.registration-form input,
.registration-form select,
.registration-form button {
  padding: 0.5rem;
  font-size: 1rem;
}
.error {
  color: red;
  font-size: 0.9rem;
}
</style>
