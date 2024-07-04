<template>
    <div class="twitter-account-evaluator">
      <h2>Twitter Account Evaluator</h2>
      <div class="input-group">
        <label>Activity Level:</label>
        <select v-model="account.activityLevel">
          <option value="inactive">Inactive</option>
          <option value="low">Low</option>
          <option value="moderate">Moderate</option>
          <option value="high">High</option>
        </select>
      </div>
      <div class="input-group">
        <label>Post Frequency:</label>
        <select v-model="account.postFrequency">
          <option value="never">Never</option>
          <option value="rarely">Rarely</option>
          <option value="occasionally">Occasionally</option>
          <option value="frequently">Frequently</option>
        </select>
      </div>
      <div class="input-group">
        <label>Follower Count:</label>
        <input type="number" v-model.number="account.followerCount" min="0">
      </div>
      <button @click="evaluateAccount">Evaluate Account</button>
      <div v-if="evaluation" class="evaluation-result">
        <h3>Evaluation Result:</h3>
        <p>{{ evaluation }}</p>
      </div>
    </div>
  </template>
  
  <script>
  import { ref, reactive } from 'vue'
  
  export default {
    setup() {
      const account = reactive({
        activityLevel: 'inactive',
        postFrequency: 'never',
        followerCount: 0
      })
  
      const evaluation = ref('')
  
      const evaluateAccount = () => {
        let score = 0
  
        // Activity Level (weight: 5)
        switch (account.activityLevel) {
          case 'inactive': score += 0; break;
          case 'low': score += 1; break;
          case 'moderate': score += 3; break;
          case 'high': score += 5; break;
        }
  
        // Post Frequency (weight: 3)
        switch (account.postFrequency) {
          case 'never': score += 0; break;
          case 'rarely': score += 1; break;
          case 'occasionally': score += 2; break;
          case 'frequently': score += 3; break;
        }
  
        // Follower Count (weight: 2)
        if (account.followerCount === 0) score += 0;
        else if (account.followerCount <= 10) score += 1;
        else score += 2;
  
        // Evaluate based on score
        if (score <= 3) {
          evaluation.value = "This account is likely a bot or inactive account."
        } else if (score <= 7) {
          evaluation.value = "This account is potentially real, but has low activity."
        } else {
          evaluation.value = "This account is likely a real, active account."
        }
      }
  
      return {
        account,
        evaluation,
        evaluateAccount
      }
    }
  }
  </script>
  
  <style scoped>
  .twitter-account-evaluator {
    max-width: 400px;
    margin: 0 auto;
    font-family: Arial, sans-serif;
  }
  
  .input-group {
    margin-bottom: 15px;
  }
  
  label {
    display: block;
    margin-bottom: 5px;
  }
  
  select, input {
    width: 100%;
    padding: 5px;
  }
  
  button {
    width: 100%;
    padding: 10px;
    background-color: #1da1f2;
    color: white;
    border: none;
    cursor: pointer;
  }
  
  .evaluation-result {
    margin-top: 20px;
    padding: 10px;
    background-color: #f0f0f0;
    border-radius: 5px;
  }
  </style>