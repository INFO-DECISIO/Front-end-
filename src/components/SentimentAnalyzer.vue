<template>
  <div class="root-container">
    <div class="sentiment-analyzer">
      <div class="content-card">
        <div class="header">
          <h1 class="title">Analyseur de Sentiment</h1>
          <div class="subtitle">Analysez le ton émotionnel de votre texte</div>
        </div>
        
        <div class="message-container">
          <textarea
            v-model="message"
            @input="onInput"
            placeholder="Tapez votre message ici..."
            class="message-input"
          ></textarea>
          <div class="character-count" :class="{ 'warning': message.length > 200 }">
            {{ message.length }} / 500
          </div>
        </div>
        
        <div class="result-container">
          <transition name="fade">
            <div v-if="sentiment" class="sentiment-result">
              <div class="emoji-container">
                <span class="emoji" v-if="sentiment === 'positif'">😊</span>
                <span class="emoji" v-else-if="sentiment === 'negatif'">😞</span>
                <span class="emoji" v-else-if="sentiment === 'neutre'">😐</span>
              </div>
              <div class="sentiment-text">
                {{ getSentimentText }}
              </div>
              <div class="sentiment-score">
                <div class="score-bar">
                  <div 
                    class="score-fill" 
                    :class="sentimentScoreClass"
                    :style="{ width: sentimentScore + '%' }"
                  ></div>
                </div>
                <div class="score-label">{{ sentimentScore }}% {{ sentimentScoreLabel }}</div>
              </div>
            </div>
            <div v-else class="waiting-prompt">
              <div class="pulse-dot"></div>
              En attente de votre message...
            </div>
          </transition>
        </div>
        
        <div class="footer">
          <div class="powered-by">Par le Groupe A </div>
          <button class="clear-button" @click="clearText" v-if="message">Effacer</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import debounce from 'lodash.debounce';

export default {
  name: 'SentimentAnalyzer',
  data() {
    return {
      message: '',
      sentiment: '',
      positiveWords: ['bien', 'heureux', 'content', 'super', 'excellent', 'génial', 'parfait'],
      negativeWords: ['triste', 'mal', 'mauvais', 'déçu', 'difficile', 'problème', 'pire']
    };
  },
  computed: {
    getSentimentText() {
      switch (this.sentiment) {
        case 'positif':
          return 'Votre message semble positif!';
        case 'negatif':
          return 'Votre message semble négatif.';
        case 'neutre':
          return 'Votre message semble neutre.';
        default:
          return '';
      }
    },
    sentimentScore() {
      switch (this.sentiment) {
        case 'positif':
          return Math.floor(70 + Math.random() * 30);
        case 'negatif':
          return Math.floor(60 + Math.random() * 40);
        case 'neutre':
          return Math.floor(40 + Math.random() * 20);
        default:
          return 0;
      }
    },
    sentimentScoreClass() {
      switch (this.sentiment) {
        case 'positif':
          return 'positive-score';
        case 'negatif':
          return 'negative-score';
        case 'neutre':
          return 'neutral-score';
        default:
          return '';
      }
    },
    sentimentScoreLabel() {
      switch (this.sentiment) {
        case 'positif':
          return 'de positivité';
        case 'negatif':
          return 'de négativité';
        case 'neutre':
          return 'de neutralité';
        default:
          return '';
      }
    }
  },
  created() {
    this.debouncedAnalyzeSentiment = debounce(this.analyzeSentiment, 300);
  },
  methods: {
    clearText() {
      this.message = '';
      this.sentiment = '';
    },
    onInput() {
      if (this.message.trim() === '') {
        this.sentiment = '';
        return;
      }
      this.debouncedAnalyzeSentiment();
    },

    analyzeSentiment() {
      const text = this.message.toLowerCase();
      
      // Cette partie sera remplacée par votre modèle IA
      // Vérifier les mots positifs
      if (this.positiveWords.some(word => text.includes(word))) {
        this.sentiment = 'positif';
        return;
      }
      
      // Vérifier les mots négatifs
      if (this.negativeWords.some(word => text.includes(word))) {
        this.sentiment = 'negatif';
        return;
      }
      
      // Si aucun mot spécifique n'est détecté 
      this.sentiment = 'neutre';
    },
  },
};
</script> 

 <!-- Script pour L'appel API
<script>
import debounce from 'lodash.debounce';
import axios from 'axios'; 

export default {
  name: 'SentimentAnalyzer',
  data() {
    return {
      message: '',
      sentiment: '',
    };
  },
  computed: {
    getSentimentText() {
      switch (this.sentiment) {
        case 'positif':
          return 'Votre message semble positif!';
        case 'negatif':
          return 'Votre message semble négatif.';
        case 'neutre':
          return 'Votre message semble neutre.';
        default:
          return '';
      }
    },
    sentimentScore() {
      switch (this.sentiment) {
        case 'positif':
          return Math.floor(70 + Math.random() * 30);
        case 'negatif':
          return Math.floor(60 + Math.random() * 40);
        case 'neutre':
          return Math.floor(40 + Math.random() * 20);
        default:
          return 0;
      }
    },
    sentimentScoreClass() {
      switch (this.sentiment) {
        case 'positif':
          return 'positive-score';
        case 'negatif':
          return 'negative-score';
        case 'neutre':
          return 'neutral-score';
        default:
          return '';
      }
    },
    sentimentScoreLabel() {
      switch (this.sentiment) {
        case 'positif':
          return 'de positivité';
        case 'negatif':
          return 'de négativité';
        case 'neutre':
          return 'de neutralité';
        default:
          return '';
      }
    }
  },
  created() {
    this.debouncedAnalyzeSentiment = debounce(this.analyzeSentiment, 300);
  },
  methods: {
    clearText() {
      this.message = '';
      this.sentiment = '';
    },
    onInput() {
      if (this.message.trim() === '') {
        this.sentiment = '';
        return;
      }
      this.debouncedAnalyzeSentiment();
    },
    
    async analyzeSentiment() {
      try {
        const response = await axios.post('URL_DE_VOTRE_API', {
          message: this.message,
        });

        // Mettre à jour le sentiment en fonction de la réponse de l'API
        this.sentiment = response.data.sentiment; // "positif", "negatif", ou "neutre"
      } catch (error) {
        console.error('Erreur lors de l\'appel à l\'API', error);
      }
    },
  },
};
</script> -->


<style>

html, body {
  margin: 0;
  padding: 0;
  height: 100%;
  width: 100%;
  font-family: 'Arial', sans-serif;
  overflow-x: hidden;
}

#app {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
}
</style>

<style scoped>

.root-container {
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: linear-gradient(135deg, #f6f8fc, #e0eafc);
  overflow: auto;
}

.sentiment-analyzer {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
}

.content-card {
  width: 100%;
  max-width: 600px;
  background-color: white;
  border-radius: 20px;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.15);
  padding: 30px;
  margin: auto;
  position: relative;
  overflow: hidden;
  transition: all 0.3s ease;
}

.content-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 15px 50px rgba(0, 0, 0, 0.2);
}

.header {
  text-align: center;
  margin-bottom: 30px;
  position: relative;
}

.header:after {
  content: '';
  position: absolute;
  bottom: -15px;
  left: 50%;
  transform: translateX(-50%);
  width: 80px;
  height: 3px;
  background: linear-gradient(90deg, #4a6fa5, #6eb9f7);
  border-radius: 3px;
}

.title {
  color: #4a6fa5;
  margin-bottom: 8px;
  font-size: 28px;
  font-weight: 700;
}

.subtitle {
  color: #8492a6;
  font-size: 16px;
}

.message-container {
  margin-bottom: 30px;
  position: relative;
}

.message-input {
  width: 100%;
  min-height: 150px;
  padding: 20px;
  border: 1px solid #e1e5ea;
  border-radius: 15px;
  font-size: 16px;
  line-height: 1.6;
  transition: all 0.3s ease;
  resize: vertical;
  font-family: 'Arial', sans-serif;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
}

.message-input:focus {
  outline: none;
  border-color: #4a6fa5;
  box-shadow: 0 0 15px rgba(74, 111, 165, 0.15);
}

.character-count {
  position: absolute;
  bottom: 10px;
  right: 15px;
  font-size: 12px;
  color: #8492a6;
  transition: all 0.3s ease;
}

.warning {
  color: #ff9800;
}

.result-container {
  display: flex;
  justify-content: center;
  min-height: 200px;
  margin-bottom: 25px;
  position: relative;
}

.sentiment-result {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  animation: fadeIn 0.5s ease;
}

.waiting-prompt {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: #8492a6;
  font-size: 16px;
  height: 150px;
}

.pulse-dot {
  width: 12px;
  height: 12px;
  border-radius: 50%;
  background-color: #4a6fa5;
  margin-bottom: 15px;
  animation: pulse 1.5s infinite;
}

.emoji-container {
  margin-bottom: 15px;
}

.emoji {
  font-size: 80px;
  animation: popIn 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

.sentiment-text {
  font-size: 20px;
  color: #4a6fa5;
  text-align: center;
  margin-bottom: 25px;
  font-weight: 600;
}

.sentiment-score {
  width: 80%;
  margin-top: 10px;
}

.score-bar {
  width: 100%;
  height: 8px;
  background-color: #e9ecef;
  border-radius: 4px;
  overflow: hidden;
  margin-bottom: 8px;
}

.score-fill {
  height: 100%;
  border-radius: 4px;
  transition: width 0.8s ease-out;
}

.positive-score {
  background: linear-gradient(90deg, #4caf50, #8bc34a);
}

.negative-score {
  background: linear-gradient(90deg, #f44336, #ff9800);
}

.neutral-score {
  background: linear-gradient(90deg, #9c9c9c, #b5b5b5);
}

.score-label {
  text-align: center;
  font-size: 14px;
  color: #566a7f;
}

.footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 20px;
  padding-top: 15px;
  border-top: 1px solid #e9ecef;
}

.powered-by {
  font-size: 12px;
  color: #8492a6;
  font-style: italic;
}

.clear-button {
  background-color: transparent;
  border: 1px solid #e1e5ea;
  color: #566a7f;
  padding: 8px 15px;
  border-radius: 20px;
  font-size: 14px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.clear-button:hover {
  background-color: #f0f2f5;
  border-color: #8492a6;
}

@keyframes popIn {
  0% {
    transform: scale(0);
    opacity: 0;
  }
  70% {
    transform: scale(1.2);
    opacity: 1;
  }
  100% {
    transform: scale(1);
  }
}

@keyframes fadeIn {
  0% {
    opacity: 0;
    transform: translateY(20px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes pulse {
  0% {
    transform: scale(0.8);
    opacity: 0.8;
  }
  50% {
    transform: scale(1.2);
    opacity: 1;
  }
  100% {
    transform: scale(0.8);
    opacity: 0.8;
  }
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}
</style>