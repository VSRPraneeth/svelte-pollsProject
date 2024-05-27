<script>
  import { tweened } from "svelte/motion";
  import Button from "../shared/Button.svelte";
  import Card from "../shared/Card.svelte";
  import PollStore from "../store/PollStore";

  export let poll;

  //reactive value
  $: totalVotes = poll.votesA + poll.votesB;
  $: percentA = ((poll.votesA * 100) / totalVotes || 0).toFixed(2);
  $: percentB = ((poll.votesB * 100) / totalVotes || 0).toFixed(2);

  // tweened percentages
  const tweenedA = tweened(0);
  const tweenedB = tweened(0);

  $: tweenedA.set(parseFloat(percentA));
  $: tweenedB.set(parseFloat(percentB));

  //handling votes
  const voteClick = (option, id) => {
    PollStore.update((currentPolls) => {
      let copiedPolls = [...currentPolls];
      let updatedPoll = copiedPolls.find((poll) => poll.id === id);
      if (option === "a") {
        updatedPoll.votesA++;
      }
      if (option === "b") {
        updatedPoll.votesB++;
      }
      return copiedPolls;
    });
  };

  const deletePoll = (id) => {
    PollStore.update((currentPolls) => {
      return currentPolls.filter((poll) => poll.id !== id);
    });
  };
</script>

<Card>
  <div class="poll">
    <h3>{poll.question}</h3>
    <p>Total votes: {totalVotes}</p>
    <!-- svelte-ignore a11y-click-events-have-key-events -->
    <div class="answer" on:click={() => voteClick("a", poll.id)}>
      <div class="percent percent-a" style="width: {$tweenedA}%;"></div>
      <div class="values-container">
        <span>{poll.answerA} ({poll.votesA})</span>
        <span>{percentA}%</span>
      </div>
    </div>
    <!-- svelte-ignore a11y-click-events-have-key-events -->
    <div class="answer" on:click={() => voteClick("b", poll.id)}>
      <div class="percent percent-b" style="width: {$tweenedB}%;"></div>
      <div class="values-container">
        <span>{poll.answerB} ({poll.votesB})</span>
        <span>{percentB}%</span>
      </div>
    </div>
    <div class="delete">
      <Button flat={true} on:click={() => deletePoll(poll.id)}>Delete</Button>
    </div>
  </div>
</Card>

<style>
  h3 {
    margin: 0 auto;
    color: #555;
  }
  p {
    margin-top: 6px;
    font-size: 14px;
    color: #aaa;
    margin-bottom: 30px;
  }
  .answer {
    background: #fafafa;
    cursor: pointer;
    margin: 10px auto;
    position: relative;
  }
  .answer:hover {
    opacity: 0.6;
  }
  .values-container {
    display: flex;
    justify-content: space-between;
  }
  span {
    display: inline-block;
    padding: 10px 20px;
  }
  .percent {
    height: 100%;
    position: absolute;
    box-sizing: border-box;
  }
  .percent-a {
    background: rgba(217, 27, 66, 0.2);
    border-left: 4px solid #d91b42;
  }
  .percent-b {
    background: rgba(69, 196, 150, 0.2);
    border-left: 4px solid #45c496;
  }
  .delete {
    margin-top: 30px;
    text-align: center;
  }
</style>
