<script lang="ts">
import { defineComponent } from 'vue'
import validator from 'validator'

export default defineComponent({
  data() {
    return {
      status: false,
      error: "",
      name: "",
      email: "",
      subject: "",
      message: ""
    };
  },
  methods: {
    async sendMessage() {
      try {
        const { name, email, subject, message } = this;
        if (!(name && email && subject && message)) {
          this.error = "Please enter all fields!";
          return;
        }
        if (!validator.isEmail(email)) {
          this.error = "Please enter valid email!";
          return;
        }
        const body = JSON.stringify({ name, email, subject, message });
        const headers = { "Content-Type": "application/json" };
        const res = await fetch("/api/message/send", { method: "POST", body, headers });

        this.status =  res.status === 201;

        if (!this.status) {
          throw new Error('Failed');
        }
      }
      catch (e) {
        this.error = "Failed to send your message, please try again later!";
      }
    },
    backToMessage() {
      this.status = false;
      this.error = '';
    },
  },
})
</script>

<template>
  <div v-if="status">
    <div class="container message">
      <div class="message-container text-center">
        <div class="image">
          <i>
            <svg xmlns="http://www.w3.org/2000/svg" width="50" height="50" fill="currentColor" class="bi bi-send" viewBox="0 0 16 16">
              <path d="M15.854.146a.5.5 0 0 1 .11.54l-5.819 14.547a.75.75 0 0 1-1.329.124l-3.178-4.995L.643 7.184a.75.75 0 0 1 .124-1.33L15.314.037a.5.5 0 0 1 .54.11ZM6.636 10.07l2.761 4.338L14.13 2.576 6.636 10.07Zm6.787-8.201L1.591 6.602l4.339 2.76 7.494-7.493Z"/>
            </svg>
          </i>
        </div>
        <div class="text">
          <h2 class="bold margin-0">Thank you!</h2>
          <p class="margin-0">Your message was sent</p>
        </div>
        <button @click="backToMessage" class="send-button">Close</button>
      </div>
    </div>
  </div>
  <div class="container" v-else>
    <h2 class="text-center">
      <i>
        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-envelope" viewBox="0 0 16 16">
          <path d="M0 4a2 2 0 0 1 2-2h12a2 2 0 0 1 2 2v8a2 2 0 0 1-2 2H2a2 2 0 0 1-2-2V4Zm2-1a1 1 0 0 0-1 1v.217l7 4.2 7-4.2V4a1 1 0 0 0-1-1H2Zm13 2.383-4.708 2.825L15 11.105V5.383Zm-.034 6.876-5.64-3.471L8 9.583l-1.326-.795-5.64 3.47A1 1 0 0 0 2 13h12a1 1 0 0 0 .966-.741ZM1 11.105l4.708-2.897L1 5.383v5.722Z"/>
        </svg>
      </i>
      New Message
    </h2>
    <label>
      <span class="bold">Name</span>
      <input type="text" id="name" name="name" placeholder="your name.." v-model="name" />
    </label>

    <label>
      <span class="bold">Email</span>
      <input type="email" id="email" name="email" placeholder="your email.." v-model="email" />
    </label>

    <label>
      <span class="bold">Subject</span>
      <input type="text" id="subject" name="subject" placeholder="provide a subject.." v-model="subject" />
    </label>

    <label>
      <span class="bold">Message</span>
      <textarea id="message" name="message" placeholder="Write something.." style="min-height: 250px"
        v-model="message"></textarea>
    </label>

    <button class="button" @click="sendMessage">Send</button>
    <div v-if="error !== ''" class="error"> {{ error }}</div>
  </div>
</template>
