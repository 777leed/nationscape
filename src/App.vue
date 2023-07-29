<template>
        <header>
            <div class="logo">
                <img src="@/assets/globe.svg" alt="LOGO">
            </div>
        </header>
        <main>
            <div class="feed-back">{{feedback}}</div>
            <div class="container" v-if="play">
                <div class="actions">
                    <div class="addition">
                        <input v-model="inputText" type="text" @keyup.enter="handleSubmit">
                        <div class="btn-sec">
                            <button @click="handleSubmit" class="sub">SUBMIT</button>
                            <button @click="handleSkip" class="skip">SKIP</button>
                        </div>
                    </div>
                </div>
                
                <div class="compo">

                    <div class="flag-container">
                        <img :src="require(`${currentFlag}`)" alt="">

                    </div>
                    <div class="st">
                        <div class="timer hyell">{{timer}}</div>
                        <div class="score hyell">{{score}}</div>
                    </div>
                    
                </div>

            </div>
            <div class="start" v-else>
                <button class="start-btn" @click="startGame">Start Round</button>
            </div>
        </main>
</template>

<script>

export default {
    data() {
        return {
            play: false, // Control variable for showing the "Start Round" button
            timer: '00:00',
            score: 0,
            cml: 0,
            correct: true,
            feedback: 'GUESS THE FLAG',
            inputText: '',
            currentFlag: null,
            // Replace the below array with a list of flag image paths
            flagImages: [
                { src: './assets/Andorra.png', value: 'Andorra', called: false },
                { src: './assets/argentina.png', value: 'argentina', called: false },
                { src: './assets/bahamas.png', value: 'bahamas', called: false },
                { src: './assets/Afghanistan.png', value: 'Afghanistan', called: false },
                { src: './assets/Albania.png', value: 'Albania', called: false },
                { src: './assets/Algeria.png', value: 'Algeria', called: false },
                { src: './assets/Angola.png', value: 'Angola', called: false },
                { src: './assets/Antigua.png', value: 'Antigua', called: false },
                { src: './assets/Armenia.png', value: 'Armenia', called: false },
                { src: './assets/Australia.png', value: 'Australia', called: false },
            ],
            currentIndex: -1,
            timerInterval: null, // Store the timer interval reference
        };
    },
    computed: {
        flags_left() {
            // Calculate the number of flags left
            return this.flagImages.filter(flag => !flag.called).length;
        },
    },
    methods: {
        convertTimeToSeconds(time) {
            const [minutes, seconds] = time.split(':').map(Number);
            return minutes * 60 + seconds;
        },
        handleSubmit() {
            if (!this.play) return; // Ensure the game has started before accepting submissions
            let userAnswer = this.inputText;
            // Check the user's answer with the corresponding value of the current flag
            if (userAnswer.toLowerCase() === this.flagImages[this.currentIndex].value.toLowerCase()) {
                this.feedback = "Correct !";
                this.correct = true;
                this.score += 10; // Add 10 to the score
                // Correct answer, move to the next flag
                this.changeFlagImage();
                this.inputText = ''; // Clear the input field after submission
            } else {
                this.feedback = "Nope !";
                this.correct = true;
            }

            if (this.flags_left > 0) {
                setTimeout(() => {
                    this.feedback = 'GUESS THE FLAG';
                    this.correct = false;
                }, 1000);
            } else {
                // No flags left, handle end of the round
                this.handleRoundEnd();
            }
        },
        handleSkip() {
            if (!this.play) return; // Ensure the game has started before allowing skips
            // Change the flag image on skip
            this.changeFlagImage();
            this.feedback = "Skipped!";
            this.correct = true;

            if (this.flags_left > 0) {
                setTimeout(() => {
                    this.feedback = 'GUESS THE FLAG';
                    this.correct = false;
                }, 1000);
            } else {
                // No flags left, handle end of the round
                this.handleRoundEnd();
            }
        },
        changeFlagImage() {
            if (this.flags_left > 0) {
                let randomIndex;
                do {
                    randomIndex = Math.floor(Math.random() * this.flagImages.length);
                } while (randomIndex === this.currentIndex || this.flagImages[randomIndex].called);

                this.currentFlag = this.flagImages[randomIndex].src;
                this.flagImages[randomIndex].called = true;
                this.currentIndex = randomIndex;
                console.log(this.flagImages[randomIndex].called);
            } else {
                // No flags left, handle end of the round
                this.handleRoundEnd();
            }
        },
        formatTime(timeInSeconds) {
            const minutes = Math.floor(timeInSeconds / 60);
            const seconds = timeInSeconds % 60;
            const formattedTime = `${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;
            return formattedTime;
        },
        handleRoundEnd() {
            // Handle what happens when the round ends (e.g., reset the game)
            clearInterval(this.timerInterval); // Stop the timer interval

            this.feedback = this.roundEnded ? 'Round Ended' : 'GUESS THE FLAG';
            this.correct = false;
            this.currentFlag = "Assets/world_bg.jpg";
            this.currentIndex = -1; // Reset currentIndex
            this.play = false; // Show the "Start Round" button again
        },
        startGame() {
            this.timer = '01:10'
            this.play = true; 
            this.feedback = 'GUESS THE FLAG'; 
            this.inputText = '';
            this.score = 0; // Reset the score for a new round
            this.currentIndex = -1; 
            this.flagImages.forEach(flag => flag.called = false);
            this.changeFlagImage(); // Show the initial flag
            this.roundEnded = false; // Mark the round as not ended yet
            let timeInSeconds = this.convertTimeToSeconds(this.timer); 
            this.timer = this.formatTime(timeInSeconds); 
            this.timerInterval = setInterval(() => {
                if (timeInSeconds > 0) {
                    timeInSeconds--;
                    this.timer = this.formatTime(timeInSeconds);
                } else {
                    // Time's up, handle end of the round
                    clearInterval(this.timerInterval);
                    this.roundEnded = true;
                    this.handleRoundEnd();
                }
            }, 1000);
        },
        
    },
};

</script>

<style>
  
  html {
      width: 100%;
      height: 100%;
  }

  body {
      margin: 0;
      padding: 0;
      width: 100%;
      height: 100%;
      
  }

  main {
      height: 80%;
      padding-right: 3%;
      padding-left: 3%;
      z-index: 1;
      display: flex;
      flex-direction: column;
      justify-content: center;
  }
  header {
      padding-top: 1%;
      padding-right: 3%;
      padding-left: 3%;
  }
  h1 {
      margin: 0;
      padding: 0;
  }
  .homescreen {
      position: relative;
      width: 100%;
      height: 100%;
      background-image: url("./assets/world_bg.jpg");
      background-position: center;
      background-size:contain;
      background-repeat: no-repeat;
  }

  .homescreen::before {
      content: "";
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background-color: rgba(9, 29, 68, 0.7); /* Adjust the opacity as needed */

  }

  .container {
      /* background-color: brown; */
      width: 100%;
      height: 100%;
      display: flex;
      justify-content: center;
      align-items:center;
      
  }
  .actions {
      /* background-color: mediumseagreen; */
      width: 50%;
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
  }
  .addition {
      /* background-color: #e25f5f; */
      width: 60%;
      height: 50%;
      display: flex;
      flex-direction: column;
      justify-content: space-evenly
  }
  .actions .addition input {
      border: 1px solid #000;
      background: #FFF;
      width: 100%;
      box-sizing: border-box;
      height: 20%;
      font-size: 1.4vw;
      text-align: center;
      font-family: Poppins;
      font-weight: 400;

  }

  .st {
      width: 60%;
      display: flex;
      align-items:center;
      justify-content: space-between;
      gap: 10px;
  }

  .st div {
      position: relative;
      border: 1px solid #000;
      text-shadow: 2px 2px #887BB0;
      text-transform: uppercase;
      box-shadow: rgba(0, 0, 0, 0.4) 0px 2px 4px, rgba(0, 0, 0, 0.3) 0px 7px 13px -3px, rgba(0, 0, 0, 0.2) 0px -3px 0px inset;
      font-size: 1.8vw;
      text-align: center;
      font-family: Poppins;
      font-weight: 400;
      border-radius: .5em;
      width: 30%;

  }

  .btn-sec {
      display: flex;
      height: 20%;
      justify-content: space-between;
  }

  .btn-sec button {
      width: 40%;
      height: 100%;
      cursor: pointer;
      color: #FFF;
      font-family: Poppins;
      font-size: 1.5vw;
      font-style: normal;
      font-weight: 700;
      line-height: normal;
      position: relative;
      border: 1px solid #000;
      text-shadow: 2px 2px #887BB0;
      text-transform: uppercase;
      box-shadow: rgba(0, 0, 0, 0.4) 0px 2px 4px, rgba(0, 0, 0, 0.3) 0px 7px 13px -3px, rgba(0, 0, 0, 0.2) 0px -3px 0px inset;
  }
  .sub {
      background-color: #69CD8E;
  }
  .skip {
      background-color: #F2AB9E;
  }
  .hyell {
      background-color: #FFB25C;
  }


  input, button {
      z-index: 1;
      border-radius: .5em;
      
  }

  input:focus {
      outline: none;
  }


  .compo {
      width: 50%;
      height: 100%;
      height: 100%;
      overflow: hidden;
      justify-content: center;
      display: flex;
      align-items: center;
      flex-direction: column;
      /* background-color: #887BB0; */
      gap: 5%;
  }

  .feed-back {
      display: flex;
      justify-content: center;
      color: #FFF;
      font-family: Poppins;
      font-size: 5.5vw;
      font-style: normal;
      font-weight: 700;
      line-height: normal;
      position: relative;
      text-shadow: 2px 2px #887BB0;
      text-transform: uppercase;
  }
  .flag-container {
      width: 60%;
      height: 50%;
      border-radius: .5em;
      /* background-color: yellow; */
      justify-content: center;
      align-items: center;
      z-index: 1;

  }
  .flag-container img {
      width: 100%;
      height: 100%;
      object-fit:cover;
      background-position: center;
      border-radius: .5em;
      border: 2px solid #FFF;
  }


  @media only screen and (max-width: 800px) {
      .container {
          /* background-color: brown; */
          width: 100%;
          height: 100%;
          display: flex;
          justify-content: center;
          align-items: center;
          flex-direction: column-reverse;
      }
    }

  .start {
    display: flex;
    justify-content: center; 
    height: 10vh;
  }
    .start-btn {
      text-align: center;
      background-color: #FFB25C;
      width: 50%;
      height: 100%;
      cursor: pointer;
      color: #FFF;
      font-family: Poppins;
      font-size: 1.5vw;
      font-style: normal;
      font-weight: 700;
      line-height: normal;
      position: relative;
      border: 1px solid #000;
      text-shadow: 2px 2px #887BB0;
      text-transform: uppercase;
      box-shadow: rgba(0, 0, 0, 0.4) 0px 2px 4px, rgba(0, 0, 0, 0.3) 0px 7px 13px -3px, rgba(0, 0, 0, 0.2) 0px -3px 0px inset;
  }

</style>
