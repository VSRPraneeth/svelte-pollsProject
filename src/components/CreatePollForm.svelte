<script>
  import { createEventDispatcher } from "svelte";
  import Button from "../shared/Button.svelte";
  import PollStore from "../store/PollStore";

  const dispatch = createEventDispatcher();
  let fields = { question: "", answerA: "", answerB: "" };
  let errors = { question: "", answerA: "", answerB: "" };
  let valid = false;

  const submitForm = () => {
    valid = true;
    // validate question
    if (fields.question.trim().length < 5) {
      errors.question = "Question must be atleast 5 characters long";
      valid = false;
    } else {
      errors.question = "";
    }

    // validate Answer A
    if (fields.answerA.trim().length < 1) {
      errors.answerA = "Answer A cannot be empty";
      valid = false;
    } else {
      errors.answerA = "";
    }
    // validate Answer B
    if (fields.answerB.trim().length < 1) {
      errors.answerB = "Answer B cannot be empty";
      valid = false;
    } else {
      errors.answerB = "";
    }

    if (valid) {
      // save poll to store
      const poll = { ...fields, votesA: 0, votesB: 0, id: Math.random() };
      PollStore.update((currentPolls) => {
        return [poll, ...currentPolls];
      });

      dispatch("addPoll");
    }
  };
</script>

<form on:submit|preventDefault={submitForm}>
  <div class="form-field">
    <label for="question">Poll Question</label>
    <input type="text" id="question" bind:value={fields.question} />
    <div class="error">{errors.question}</div>
  </div>
  <div class="form-field">
    <label for="answer-a">Answer A</label>
    <input type="text" id="answer-a" bind:value={fields.answerA} />
    <div class="error">{errors.answerA}</div>
  </div>
  <div class="form-field">
    <label for="answer-b">Answer B</label>
    <input type="text" id="answer-b" bind:value={fields.answerB} />
    <div class="error">{errors.answerB}</div>
  </div>
  <Button type="secondary" flat={true}>Add Poll</Button>
</form>

<style>
  form {
    width: 400px;
    margin: 0 auto;
    text-align: center;
  }
  .form-field {
    margin: 18px auto;
  }
  input {
    width: 100%;
    border-radius: 6px;
  }
  label {
    margin: 10px auto;
    text-align: left;
  }
  .error {
    font-weight: bold;
    font-size: 12px;
    color: #d91b42;
  }
</style>
