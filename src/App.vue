<template>
  <div id="app">
    <header class="col-12 allign-items-center">
      <h1>Get your daily trump quotes!</h1>
    </header>

    <div class="col-md-8 col-sm-10 col-lg-6 mx-auto quote">
      <trump-quote :quote="quote" @get:newQuote="handleNewQuote" @get:personalQuote="handleNewPersonalQuote"/>
    </div>
    <div class="col-md-6 col-sm-8 mx-auto history">
      <history-table :historyData="historyData"/>
    </div>
  </div>
</template>

<script>
import TrumpQuote from '@/components/TrumpQuote.vue'
import HistoryTable from '@/components/HistoryTable.vue'

export default {
  name: 'app',
  components: {
    TrumpQuote,
    HistoryTable
  },
  data() {
    return {
      quote: '',
      historyData: [],
      ready: true,
    }
  },
  mounted() {
    this.getQuote(),
    this.getPersonalQuote()
  },
  methods: {
    /**
     * async method gets a new trump quote from api
     */
    async getQuote() {
      this.ready = false
            try {
                const response = await fetch('https://api.whatdoestrumpthink.com/api/v1/quotes/random')
                const data = await response.json()
                this.quote = data.message
            } catch (error) {
                console.error(error)
            }
      this.ready = true
    },

    /**
     * Gets a personalized quote
     * @param name name to be used in query
     * 
     */
    async getPersonalQuote(name) {
      this.ready = false
      try {
        const response = await fetch('https://api.whatdoestrumpthink.com/api/v1/quotes/personalized?q=' + name)
        const data = await response.json()
        this.quote = data.message
        } catch (error) {
          console.error(error)
          }
      this.ready = true
      
    },
    /**
     * Handles a new quote request
     * @param oldQuote original quote for history saving
     */
    handleNewQuote(oldQuote) {
      if (this.ready){
      this.AddQuoteToHistory(oldQuote);

      this.getQuote();
      }
    },

    /**
     * Handles a new personal quote request
     * @param container contains the old quote and the query name
     */
    handleNewPersonalQuote(container) {
      if (this.ready){
      this.AddQuoteToHistory(container.quote);

      this.getPersonalQuote(container.name);
      }
    },

    /**
     * Adds given quote to history table
     * @param oldQuote quote to be added
     */
    AddQuoteToHistory(oldQuote) {
      // when the user makes to many requests prevents duplicate entries.
      this.historyData.forEach(historyEntry => {
        if (historyEntry.quote === oldQuote){
          return
        }
      });
      const id = this.historyData.length + 1;
      const newHistoryEntry = {
        id: id,
        quote: oldQuote,
      }
      

      this.historyData = [newHistoryEntry, ...this.historyData]
    }


  }
}
</script>

<style>

header {
  text-align: center;
  margin-top: 5vh;
  margin-bottom: 25vh;
  
}
.quote {
  min-height: 40vh;

}

.history {
  margin-top: 25vh;
}

#history-table {
    height: 15vh;
}

</style>
