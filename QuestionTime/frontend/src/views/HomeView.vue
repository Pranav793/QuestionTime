<template>
  <div class="home mt-3">
    <div class="container">
      <div v-for="question in questions" :key="question.uuid">
        <div class="card shadow p-2 mb-4 bg-body rounded">
          <div class="card-body">
            <p class="mb-0">
              Posted by:
              <span class="question-author">{{ question.author }}</span>
            </p>
            <h2><router-link class="question-link" :to="{ name: 'question', params: {slug: question.slug} }">{{question.content}}</router-link></h2>
            <!-- {{ question.content }} -->
            <p class="mb-0">Answers: {{ question.answers_count }}</p>
          </div>
        </div>
      </div>

      <div class="my-4">
        <p v-show="loadingQuestion">...loading...</p>
        <button
          v-show="next"
          @click="getQuestions"
          class="btn btn-sm btn-outline-success"
        >
          Load More
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { axios } from "@/common/api.service.js";
export default {
  name: "HomeView",
  data() {
    return {
      questions: [],
      next: null,
      loadingQuestion: false,
    };
  },
  methods: {
    async getQuestions() {
      let endpoint = "/api/v1/questions/";
      if (this.next) {
        endpoint = this.next;
      }
      this.loadingQuestion = true; // loading button shows
      try {
        const response = await axios.get(endpoint);
        this.questions.push(...response.data.results); // pushing to the end of the array, basically join
        this.loadingQuestion = false; // loading button off
        if (response.data.next) {
          this.next = response.data.next;
        } else {
          this.next = null;
        }
      } catch (error) {
        console.log(error.response);
        alert(error.response.statusText);
      }
    },
  },
  created() {
    console.log("Created lifecycle hook");
    document.title = "QuestionTime"
    this.getQuestions();
  },
};
</script>


<style>
.question-author {
  font-weight: bold;
  color: #dc3545;
}

.question-link {
  font-weight: 400;
  color: black;
  text-decoration: none;
}

.question-link:hover {
  color: #343a40;
}
</style>