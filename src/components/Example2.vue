<template>
    <div class="twubric-container">
      <div v-if="loading">Loading...</div>
      <div v-else-if="error">Error: {{ error }}</div>
      <div v-else>
        <div class="sort-section">
    
          <div class="sort-buttons">
            <button @click="sort('total')">Twubric Score</button>
            <button @click="sort('friends')">Friends</button>
            <button @click="sort('influence')">Influence</button>
            <button @click="sort('chirpy')">Chirpiness</button>
          </div>
        </div>
  
        <div class="date-filter">
          
          <div class="date-inputs">
            <div class="date-input">
              <label>Start Date</label>
              <input type="date" v-model="startDate" @change="filterByDate">
            </div>
            <div class="date-input">
              <label>End Date</label>
              <input type="date" v-model="endDate" @change="filterByDate">
            </div>
          </div>
        </div>
  
        <div class="user-grid">
          <div v-for="user in sortedUsers" :key="user.uid" class="user-card">
            <div class="user-name">{{ user.fullname }}</div>
            <div class="twubric-score">{{ user.twubric.total }}</div>
            <div class="twubric-details">
              <div class="twubric-item">
                <span>{{ user.twubric.friends }}</span>
                <label>Friends</label>
              </div>
              <div class="twubric-item">
                <span>{{ user.twubric.influence }}</span>
                <label>Influence</label>
              </div>
              <div class="twubric-item">
                <span>{{ user.twubric.chirpy }}</span>
                <label>Chirpiness</label>
              </div>
            </div>
            <div class="join-date">{{ formatDate(user.join_date) }}</div>
            <button @click="removeUser(user.uid)" class="remove-btn">Remove</button>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'TwubricUI',
    data() {
      return {
        users: [],
        sortKey: 'total',
        sortOrder: 1,
        startDate: null,
        endDate: null,
        loading: true,
        error: null,
      };
    },
    computed: {
      sortedUsers() {
        return this.users
          .filter(this.dateFilter)
          .sort((a, b) => {
            let aValue = this.sortKey === 'total' ? a.twubric.total : a.twubric[this.sortKey];
            let bValue = this.sortKey === 'total' ? b.twubric.total : b.twubric[this.sortKey];
            return (aValue - bValue) * this.sortOrder;
          });
      },
    },
    mounted() {
      this.fetchUsers();
    },
    methods: {
      async fetchUsers() {
        const apiUrl = 'https://gist.githubusercontent.com/pandemonia/21703a6a303e0487a73b2610c8db41ab/raw/82e3ef99cde5b6e313922a5ccce7f38e17f790ac/twubric.json';
  
        try {
          const response = await fetch(apiUrl);
          if (!response.ok) {
            throw new Error('Network response was not ok');
          }
          this.users = await response.json();
          this.loading = false;
        } catch (error) {
          this.error = error.message;
          this.loading = false;
        }
      },
      sort(key) {
        if (this.sortKey === key) {
          this.sortOrder *= -1;
        } else {
          this.sortKey = key;
          this.sortOrder = 1;
        }
      },
      filterByDate() {
        // Method to trigger computed property update
      },
      dateFilter(user) {
        if (!this.startDate && !this.endDate) return true;
        let joinDate = new Date(user.join_date);
        let start = this.startDate ? new Date(this.startDate) : null;
        let end = this.endDate ? new Date(this.endDate) : null;
        return (!start || joinDate >= start) && (!end || joinDate <= end);
      },
      removeUser(uid) {
        this.users = this.users.filter(user => user.uid !== uid);
      },
      formatDate(dateString) {
        return new Date(dateString).toLocaleDateString('en-US', { month: 'short', day: 'numeric', year: 'numeric' });
      },
    },
  };
  </script>
  
  <style scoped>
  .twubric-container {
    font-family: Arial, sans-serif;
    max-width: 1200px;
    margin: 0 auto;
    padding: 20px;
  }
  
  .sort-section, .date-filter {
    margin-left: 400px;
    margin-bottom: 20px;
    gap: 20px;
  }
  
  .sort-buttons {
    display: flex;
    gap: 10px;
  }
  
  .sort-buttons button {
    padding: 10px;
    background-color: #f0f0f0;
    border: 1px solid #ddd;
    cursor: pointer;
  }
  
  .date-inputs {
    display: flex;
    gap: 20px;
    margin-left: 60px;
  }
  
  .date-input {
    display: flex;
    flex-direction: column;
  }
  
  .user-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 20px;
  }
  
  .user-card {
    border: 1px solid #ddd;
    padding: 15px;
    display: grid;
    grid-template-columns: 2fr 1fr;
    grid-template-areas:
      "name score"
      "details details"
      "date remove";
    align-items: center;
  }
  
  .user-name {
    grid-area: name;
    font-weight: bold;
  }
  
  .twubric-score {
    grid-area: score;
    font-size: 24px;
    font-weight: bold;
    text-align: right;
  }
  
  .twubric-details {
    grid-area: details;
    display: flex;
    justify-content: space-between;
    margin: 10px 0;
  }
  
  .twubric-item {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  
  .twubric-item span {
    font-weight: bold;
  }
  
  .join-date {
    grid-area: date;
  }
  
  .remove-btn {
    grid-area: remove;
    background-color: #ff4136;
    color: white;
    border: none;
    padding: 5px 10px;
    cursor: pointer;
    justify-self: end;
  }
  </style>
  