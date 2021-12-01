<template>
  <flow-form v-bind:questions="questions" v-bind:progressbar="true" @answer="onAnswer" />
</template>

<script>
import FlowForm, {
  QuestionModel,
  QuestionType,
  ChoiceOption,
} from "@ditdot-dev/vue-flow-form";

export default {
  name: "App",
  components: {
    FlowForm,
  },
  data() {
    return {
      questions: [
        // QuestionModel array
        new QuestionModel({
          title: "Question",
          type: QuestionType.MultipleChoice,
          multiple: false,
          required: true,
          options: [
            new ChoiceOption({
              label: "Answer 1",
              correct: true
            }),
            new ChoiceOption({
              label: "Answer 2",
            }),
            new ChoiceOption({
              label: "Answer 3",
            }),
            new ChoiceOption({
              label: "Answer 4",
            }),
          ],
        }),
      ],
    };
  },
  methods: {
    onAnswer(questionAnswered) {
      console.log(questionAnswered);
    }
  },
};
</script>

<style>
/* Import Vue Flow Form base CSS */
@import "~@ditdot-dev/vue-flow-form/dist/vue-flow-form.css";
/* Import one of the Vue Flow Form CSS themes (optional) */
@import "~@ditdot-dev/vue-flow-form/dist/vue-flow-form.theme-minimal.css";
/* @import '~@ditdot-dev/vue-flow-form/dist/vue-flow-form.theme-green.css'; */
/* @import '~@ditdot-dev/vue-flow-form/dist/vue-flow-form.theme-purple.css'; */
</style>
