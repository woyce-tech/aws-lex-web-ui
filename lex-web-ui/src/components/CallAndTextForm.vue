<template>
  <div class="form-container">
    <div class="form-header">
      <h2>{{ activeTab === 2 ? "Send us a text" : "Request a callback" }}</h2>
      <p>Send any details you'd like, and we'll get back to you shortly</p>
      <button class="close-button" @click="closeForm">&times;</button>
    </div>
    <div class="form-content">
      <form @submit.prevent="handleSubmit">
        <div class="form-row">
          <div class="form-group half">
            <input
              v-model="formData.firstName"
              type="text"
              placeholder="First Name"
              :class="{ error: errors.firstName }"
            />
          </div>
          <div class="form-group half">
            <input
              v-model="formData.lastName"
              type="text"
              placeholder="Last Name"
              :class="{ error: errors.lastName }"
            />
          </div>
        </div>
  
  
        <div class="form-group">
          <input
            v-model="formData.phone"
            type="text"
            placeholder="Cell Phone *"
            :class="{ error: errors.phone }"
            required
          />
        </div>
  
        <div class="form-group">
          <textarea
            v-model="formData.message"
            placeholder="Message *"
            rows="4"
            :class="{ error: errors.message }"
            required
          ></textarea>
        </div>
        <p class="disclaimer">
          By submitting, you agree to be contacted about your request & other
          information using automated technology. Message frequency varies. Msg
          & data rates may apply. Text STOP to cancel.
          <a href="#" class="policy-link">Acceptable Use Policy</a>
        </p>
      </form>
    </div>
    <div class="custom-divider"></div>
    <div class="form-footer">
          <div class="button-group">
            <button type="button" class="btn-cancel" @click="closeForm">
              Cancel
            </button>
            <button type="submit" class="btn-send">Send</button>
          </div>
        </div>
  </div>
</template>

<script>
export default {
  name: "ContactForm",
  props: {
    activeTab: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      formData: {
        firstName: "",
        lastName: "",
        // email: "",
        phone: "",
        // subject: "",
        message: "",
      },
      errors: {
        firstName: false,
        lastName: false,
        // email: false,
        phone: false,
        // subject: false,
        message: false,
      },
    };
  },
  methods: {
    validateForm() {
      console.log('------------this.formData', this.formData)
      let isValid = true;

      // Reset errors
      Object.keys(this.errors).forEach((key) => {
        this.errors[key] = false;
      });

      // Email validation
      // const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      // if (!emailRegex.test(this.formData.email)) {
      //   this.errors.email = true;
      //   isValid = false;
      // }

      // Phone validation
      const phoneRegex = /^\d{10}$/;
      if (!phoneRegex.test(this.formData.phone.replace(/\D/g, ""))) {
        this.errors.phone = true;
        isValid = false;
      }

      // Required fields validation
      // if (!this.formData.subject.trim()) {
      //   this.errors.subject = true;
      //   isValid = false;
      // }

      if (!this.formData.message.trim()) {
        this.errors.message = true;
        isValid = false;
      }

      return isValid;
    },
    handleSubmit() {
      if (this.validateForm()) {
        // Handle form submission
        console.log("Form submitted asdf asdf asdf:", this.formData, this.activeTab);
        const message = {
          type: "human",
          template:'from',
          fromType:this.activeTab === 2 ? 'sms' : 'call',
          text: JSON.stringify(this.formData),
        };

        this.$store.dispatch("postTextMessage", message).then(() => {
          console.log('from-submission done')
        });
        // You would typically make an API call here
        this.$emit("form-submitted", this.formData);
        this.$emit("closeForm");
      }
    },
    closeForm() {
      this.$emit("closeForm");
    },
  },
};
</script>

<style scoped>
.form-container {
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  margin-bottom: 10px;
  background-color: #fff;
  border-radius: 1rem;
  height: 90cqh;
  overflow-y: auto;
}

.form-header {
  position: relative;
  background-color: #f8f8fd;
  padding: 20px;
  border-top-left-radius: 1rem;
  border-top-right-radius: 1rem;
}

.form-header h2 {
  margin: 0 0 8px 0;
  font-size: 1.5rem;
  text-align: start;
}

.form-header p {
  margin: 0;
  color: #666;
  text-align: start;
}

.custom-divider {
  height: 1px;
  background-color: #ccc;
  width: 100%;
}

.close-button {
  position: absolute;
  top: 10px;
  right: 24px;
  background: none;
  border: none;
  font-size: 28px;
  cursor: pointer;
  padding: 0;
  color: #666;
}

.form-content {
  /* max-height: 59vh; */
  /* overflow-y: auto; */
  padding: 20px;
}

.form-group {
  margin-bottom: 15px;
}

.form-row {
  display: flex;
  gap: 15px;
  margin-bottom: 15px;
}

.half {
  flex: 1;
}

input, textarea {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 14px;
}

input.error, textarea.error {
  border-color: #dc3545;
}

textarea {
  resize: vertical;
}

.form-footer {
  padding: 20px;
}

.disclaimer {
  font-size: 12px;
  color: #666;
  margin-bottom: 20px;
}

.policy-link {
  color: #800080;
  text-decoration: none;
}

.button-group {
  display: flex;
  gap: 10px;
  justify-content: flex-end;
  width: 100%;
}

.btn-cancel, .btn-send {
  padding: 10px 20px;
  border-radius: 4px;
  border: none;
  cursor: pointer;
  font-weight: 500;
  width: 100%;
}

.btn-cancel {
  background: #fff;
  border: 1px solid #ddd;
}

.btn-send {
  background: #800080;
  color: white;
}

/* Required field indicator */
input[required]::placeholder,
textarea[required]::placeholder {
  position: relative;
}

input[required]::placeholder::after,
textarea[required]::placeholder::after {
  content: '*';
  color: #dc3545;
  margin-left: 2px;
}
/* Scrollbar width */
.form-content::-webkit-scrollbar {
  width: 8px; /* Adjust scrollbar width */
}

/* Scrollbar track (background) */
.form-content::-webkit-scrollbar-track {
  background: #f1f1f1; /* Light gray background */
  border-radius: 10px;
}

/* Scrollbar handle (thumb) */
.form-content::-webkit-scrollbar-thumb {
  background: #c0c0c0; /* Dark gray color */
  border-radius: 10px;
}
/* @media screen and (min-height: 0px) and (max-height: 460px) {
    .form-container {
      height: 82vh !important;
      overflow-y: auto;
    }
  }
@media screen and (min-height: 460px) and (max-height: 630px) {
  .form-container {
    height: 84vh !important;
    overflow-y: auto;
  }
}
@media screen and (max-height: 720px) {
  .form-container {
    height: 89vh;
    overflow-y: auto;
  }
} */
</style>
