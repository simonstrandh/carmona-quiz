<template>
  <div>
    <div id="header">
      Carmona Quiz
    </div>
    <flow-form
      v-if="formFound"
      ref="flowform"
      :questions="questions"
      :progressbar="true"
      :language="language"
      :standalone="true"
      :timer="true"
      @submit="onSubmit"
      @complete="onComplete"
      @timer="onTimer"
    >
      <template #complete>
        <p>
          <span
            class="fh2"
          >Bra jobbat!</span>
          <span
            v-if="!submitted"
            class="f-section-text"
          >
            Du kan gå tillbaka och ändra dina svar eller klicka på nästa för att
            få ditt resultat :)
          </span>
        </p>
      </template>

      <template #completeButton>
        <div
          v-if="!submitted"
          class="f-submit"
        >
          <button
            ref="button"
            class="o-btn-action"
            type="submit"
            href="#"
            aria-label="Press to submit"
            @click.prevent="onSubmit()"
          >
            <span>Nästa</span>
          </button>
        </div>
        <p 
          v-if="submitted && time"
          class="text-success"
        >
          Din tid: {{ formattedTime }}
        </p>
        <p
          v-if="submitted"
          class="text-success"
        >
          Du fick {{ score }} av {{ total }} rätt.
        </p>
      </template>
    </flow-form>
    <div
      v-else
      id="no-form"
    >
      <div>No form found!</div>
    </div>
  </div>
</template>

<script>
import FlowForm, {
  QuestionModel,
  QuestionType,
  ChoiceOption, //eslint-disable-line
  LanguageModel,
} from "@ditdot-dev/vue-flow-form";

import quiz from "./forms/quiz.json";

export default {
  name: "App",
  components: {
    FlowForm,
  },
  data() {
    return {
      submitted: false,
      completed: false,
      formFound: false,
      score: 0,
      total: 0,
      time: 0,
      formattedTime: "",
      language: new LanguageModel({
        enterKey: "Enter",
        shiftKey: "Shift",
        ok: "OK",
        continue: "Fortsätt",
        skip: "Hoppa över",
        pressEnter: "Tryck :enterKey",
        multipleChoiceHelpText: "Välj flera svar",
        multipleChoiceHelpTextSingle: "Välj ett svar",
        otherPrompt: "Annan",
        placeholder: "Skriv ditt svar här...",
        submitText: "Skicka",
        longTextHelpText: ":shiftKey + :enterKey för att göra ett radbryt.",
        prev: "Föregående",
        next: "Nästa",
        percentCompleted: ":percent% klart",
        invalidPrompt: "Ogiltig inmatning i formuläret",
        thankYouText: "Tack!",
        successText: "Dina svar är inskickde",
        ariaOk: "Tryck för att fortsätta",
        ariaRequired: "Obligatoriskt steg",
        ariaPrev: "Föregående steg",
        ariaNext: "Nästa steg",
        ariaSubmitText: "Tryck för att skicka in",
        ariaMultipleChoice: "Tryck på :letter för att välja",
        ariaTypeAnswer: "Skriv ditt svar här",
      }),
      questions: [],
    };
  },
  mounted() {
    const urlParams = new URLSearchParams(window.location.search);
    const formId = urlParams.get("formId");
    this.getForm(formId);
  },
  methods: {
    onSubmit() {
      this.$refs.flowform.submitted = true;

      this.submitted = true;
      this.calculateScore();
    },
    onTimer(time, formattedTime) {
      this.time = formattedTime;
      this.formattedTime = formattedTime;
    },
    calculateScore() {
      this.questions.forEach((question) => {
        if (question.type !== QuestionType.SectionBreak) {
          let answer = question.answer;
          if (answer === true) {
            this.score++;
          }
        }
      });
    },
    onComplete(completed, questionList) {
      //eslint-disable-line
      console.log(questionList);
      this.completed = completed;
    },
    getForm(formId) {
      let form = quiz[formId];
      this.total = form.questions.length;
      if (form) {
        this.questions.push(
          new QuestionModel({
            title: form.title,
            tagline: this.total + (this.total == 1 ? " fråga" : " frågor"),
            subtitle: form.subtitle,
            description: form.description,
            type: QuestionType.SectionBreak,
            required: true,
          })
        );

        form.questions.forEach((question) => {
          let title = question.title;

          let options = question.options.map((option) => {
            return new ChoiceOption({
              label: option.label,
              value: option.value
            });
          });

          this.questions.push(
            new QuestionModel({
              title: title,
              type: QuestionType.MultipleChoice,
              multiple: false,
              required: true,
              options: options,
            })
          );
        });

        this.formFound = true;
      } else {
        this.formFound = false;
      }
    },
  },
};
</script>

<style>
/* Import Vue Flow Form base CSS */
@import "~@ditdot-dev/vue-flow-form/dist/vue-flow-form.css";
/* Import one of the Vue Flow Form CSS themes (optional) */
/* @import "~@ditdot-dev/vue-flow-form/dist/vue-flow-form.theme-minimal.css"; */
/* @import '~@ditdot-dev/vue-flow-form/dist/vue-flow-form.theme-green.css'; */
@import "~@ditdot-dev/vue-flow-form/dist/vue-flow-form.theme-purple.css";

#header {
  width: 100%;
  height: 75px;
  background: blueviolet;
  font-family: Arial, Helvetica, sans-serif;
  font-weight: bold;
  color: white;
  font-size: 35px;
  display: flex;
  align-items: center;
  justify-content: center;
}

#no-form {
  font-family: Arial, Helvetica, sans-serif;
  font-size: 35px;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 100px;
}
</style>
