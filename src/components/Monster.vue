<template>
  <div class="monster game-room container" v-if="roomInfo">

    <div class="btn-container" style>
      <div class="row mb-4">
        <div class="btn-group col-sm" role="group" aria-label="Deck Controls">
          <button class="btn btn-outline-dark" v-on:click="previousCard()" :disabled="roomInfo.xCardIsActive || roomInfo.currentCardIndex == 0">Previous</button>
          <button class="btn btn-outline-dark" v-on:click="xCard()" :disabled="roomInfo.currentCardIndex == 0">Pause</button>
          <button class="btn btn-outline-dark" v-on:click="nextCard()" :disabled="roomInfo.xCardIsActive || roomInfo.currentCardIndex == gSheet[gSheet.length-1].ordered">
            <span v-if="roomInfo.currentCardIndex == 0">Start</span>
            <span v-if="roomInfo.currentCardIndex !== 0">Next</span>
          </button>
        </div>
      </div>
    </div>

    <!--<h1 class="">{{roomInfo.roundTitle}}</h1>-->
    <h3 class="mb-4">{{roomInfo.roundInfo}} <span class="">{{roomInfo.roundProgress}}</span></h3>


    <div v-if="roomInfo.xCardIsActive" class="mb-4">
      <transition name="fade">
        <div class="card d-flex align-items-center shadow">
          <div class="card-title">
            <h1 class="mt-5">Pause</h1>
          </div>
          <div class="card-body align-items-center d-flex justify-content-center">
            <h4>
              Talk about the direction of story, or revise some content, or adjust the tone. Once everyone is on the same page, resume play.
            </h4>
          </div>

          <button class="btn btn-outline-dark mb-5" style="width:100px;" type="button" v-on:click="xCard()" :disabled="roomInfo.currentCardIndex == 0">Continue</button>
        </div>
      </transition>
    </div>


    <div v-if="!roomInfo.xCardIsActive">
      <div v-for="(row, index) in gSheet" v-bind:key="index">
        <transition name="fade">
          <div class="row mb-4" v-if="row.ordered == roomInfo.currentCardIndex">
            <div class="col-sm">
              <div class="card shadow" v-on:click="updateClickedCard(index)" style="cursor:pointer">
                <div class="card-body ">

                    <div class="card-title" style="white-space: pre-line" v-if="!row.subtitle">
                      <div v-if="index == 0">
                        <h1 class="mt-4">{{row.archetype}}</h1>
                        <h2>{{row.characterDetail}}</h2>
                      </div>

                      <div v-if="index !== 0">
                        <p class="mt-4" style="white-space: pre-line" v-html="row.archetype"></p>
                        <div class="text-left" style="white-space: pre-line" v-html="row.characterDetail">
                          
                        </div>
                      </div>

                    </div>

                    <h4 class="card-title" style="white-space: pre-line" v-if="row.subtitle">
                      {{row.archetype}}
                    </h4>
                    <h5 class="card-subtitle mb-4 text-muted">{{row.subtitle}}</h5>

                    <div class="card-text text-left" v-if="clickedCard == index || roomInfo.currentCardIndex == gSheet[gSheet.length-1].ordered" style="white-space: pre-line">

                      <h5>{{row.characterQuestion}}</h5>
                      <p>
                        {{row.characterDetail}}
                      </p>
                      <h5 class="mt-4">{{row.keyQuestion}}</h5>
                      <p>
                        {{row.keyDetails}}
                      </p>
                    </div>

                    <svg v-if="clickedCard !== index && row.characterQuestion" width="1em" height="1em" viewBox="0 0 16 16" class="bi bi-plus-circle" fill="currentColor" xmlns="http://www.w3.org/2000/svg">
                      <path fill-rule="evenodd" d="M8 15A7 7 0 1 0 8 1a7 7 0 0 0 0 14zm0 1A8 8 0 1 0 8 0a8 8 0 0 0 0 16z"/>
                      <path fill-rule="evenodd" d="M8 4a.5.5 0 0 1 .5.5v3h3a.5.5 0 0 1 0 1h-3v3a.5.5 0 0 1-1 0v-3h-3a.5.5 0 0 1 0-1h3v-3A.5.5 0 0 1 8 4z"/>
                    </svg>


                </div>
              </div>
            </div>
          </div>
        </transition>
      </div>
    </div>

    <!-- these are advice and pause buttons, feel free to add them back. The current advice is geared toward Dawn of the Monster Invasion.
    
    <div class="btn-group col-sm" role="group" aria-label="Advice Buttons">
      <b-button v-b-modal.speaker-tips variant="warning">Speaker Tips</b-button>

      <b-modal id="speaker-tips" title="Advice for giving a speech" hide-footer>
        <div class="d-block text-left">
          <h4>Creating a character</h4>
          <p>Take a minute to read through the suggested answers. For each question, pick one or two answers that resonate with you or make up your own; consider what embellishments you might add. Think about what type of personality your character might have and how that might be expressed through manner of speech and gestures.</p>

          <h4>Opening remarks</h4>
          <p>When you're ready to start the meeting, you can say "May I have your attention please." Introduce yourself, in character, including any of the key details about your background. Then, make sure you cover the key information relating to the second question on the character card.</p>
          <p>Feel free to ham it up and make your character ridiculous! When you're done with your speech, open it up to the audience with "Any questions?"</p>

          <h4>Answering questions</h4>
          <p>Here are some go-to tactics when answering questions:</p>
          <ul>
            <li>Make up an answer</li>
            <li>Change the subject</li>
            <li>Be overly defensive</li>
            <li>Show contrition</li>
            <li>Attack whomever asked the question</li>
            <li>Claim ignorance</li>
            <li>Cut off whomever is asking the questions</li>
          </ul>
          <p>You end the round at any time by saying "That will be all!"</p>
        </div>
      </b-modal>

      <b-button v-b-modal.audience-tips variant="warning">Audience Tips</b-button>

      <b-modal id="audience-tips" title="Advice for asking questions" hide-footer>
        <div class="d-block text-left">
          <h4>Being a character</h4>
          <p>You don't need to ask questions in character but it can help! Consider who you might be and why you're at the this speech. For instance you could be a collegue, a rival, a skeptic, a fan, or a concerned citizen.</p>

          <h4>Asking questions</h4>
          <p>Try keep the focus on the speaker; don't hog the spotlight. Try the following types of questions:</p>
          <ul>
            <li>Ask for an elaboration on something the speaker mentioned</li>
            <li>Ask about potential consequences of something in the story</li>
            <li>Cast doubt on the speaker: "That's not what I heard..."</li>
            <li>Introduce new info: “What do you think of rumors that...”</li>
            <li>Share a controverisal opinion: “More of a statement than a question...”</li>
          </ul>

        </div>
      </b-modal>

      <button class="btn btn-warning" v-on:click="xCard()" :disabled="roomInfo.currentCardIndex == 0">Pause</button>
    </div>
    -->

  </div>
</template>

<script>
import { roomsCollection } from '../firebase';
import axios from 'axios'

export default {
  name: 'app-monster',
  props: {
    roomID: String,
    gSheetID: String
  },
  data: function(){
    return {
      roomInfo: {
        currentCardIndex: 0,
        xCardIsActive: false,
        cardSequence: [0,1,2],
        roundInfo: "",
        roundProgress: "",
        roundTitle: ""
      },
      dataReady: false,
      gSheet: [{text:"loading"}],
      orderedCards: [],
      unorderedCards: [],
      clickedCard: -1,
      instructionCardCount: 15,
      gameRoundCount: 6
    }
  },
  mounted(){
    this.fetchAndCleanSheetData(this.gSheetID);

    this.$bind('roomInfo', roomsCollection.doc(this.roomID))
      .then(() => {
        this.dataReady = true
      })
      .then(() => {
        if (!this.roomInfo){
          console.log("new room!")

          roomsCollection.doc(this.roomID).set({currentCardIndex:0,xCardIsActive: false, cardSequence:[0,1,2]})
        }
      })
      .catch((error) => {
        console.log('error in loading: ', error)
      })
  },
  methods: {
    previousCard(){
      roomsCollection.doc(this.roomID).update({
        currentCardIndex: this.roomInfo.currentCardIndex -= 1
      })
      this.updateRoundInfo();
    },
    nextCard(){
      roomsCollection.doc(this.roomID).update({
        currentCardIndex: this.roomInfo.currentCardIndex += 1
      })
      this.updateRoundInfo();
    },
    updateRoundInfo(){
      var newRoundInfo = ""
      var newRoundProgress =""

      if (this.roomInfo.currentCardIndex == 0 || this.roomInfo.currentCardIndex == (this.instructionCardCount + this.gameRoundCount + 1)){
        newRoundInfo = ""
      } else if (this.roomInfo.currentCardIndex <= this.instructionCardCount){
        newRoundInfo = "Instructions";
        newRoundProgress = (this.roomInfo.currentCardIndex) + " of " + this.instructionCardCount
      //} else if (this.roomInfo.currentCardIndex > this.instructionCardCount) {
        //newRoundInfo = "Round";
        //newRoundProgress = (this.roomInfo.currentCardIndex - this.instructionCardCount) + " of " + this.gameRoundCount
      } else {
        newRoundInfo = ""
      }

      var newRoundTitle = ""
      /* For the published version, this section adds round titles

      if (this.roomInfo.currentCardIndex > this.instructionCardCount) {
        switch (this.roomInfo.currentCardIndex - this.instructionCardCount){
          case 1:
            newRoundTitle = "A Glimpse of Trouble"
            break;
          case 2:
            newRoundTitle = "A Clueless Public"
            break;
          case 3:
            newRoundTitle = "It's Here!"
            break;
          case 4:
            newRoundTitle = "Not Helping..."
            break;
          case 5:
            newRoundTitle = "Down to the Wire"
            break;
          case 6:
            newRoundTitle = "It's All Over"
            break;
        }
      } else {
        newRoundTitle = ""
      }
      */

      roomsCollection.doc(this.roomID).update({
        roundInfo: newRoundInfo,
        roundProgress: newRoundProgress,
        roundTitle: newRoundTitle
      })
    },
    xCard(){
      roomsCollection.doc(this.roomID).update({
        xCardIsActive: !this.roomInfo.xCardIsActive
      })
    },
    updateClickedCard(index){
      if(this.clickedCard == index){
        this.clickedCard=-1
      } else if (index !== 0 && index > this.instructionCardCount) {
        this.clickedCard=index
      }
    },
    fetchAndCleanSheetData(sheetID){
      if (!sheetID || sheetID == 'demo') {
        sheetID = '1NgNHy7Qe1R8KhGR2cOmJwL2aOl2tocBemW2HIAKjrvI'
      }

      var getURL = 'https://sheets.googleapis.com/v4/spreadsheets/' + sheetID + '?includeGridData=true&ranges=a1:aa100&key=AIzaSyDsIM5nJ3hNoVRCSd3kJXfrAL8_n9gwFdM'

      axios.get(getURL)
      .then(response => {

        var cleanData = []
        var gRows = response.data.sheets[0].data[0].rowData

        // Transform Sheets API response into cleanData
        gRows.forEach((item, i) => {
          if (i !== 0 && item.values[0] && item.values[0].formattedValue){

            var rowInfo = {
              ordered: item.values[0].formattedValue,
              archetype: item.values[1].formattedValue,
              subtitle: item.values[2].formattedValue,
              characterQuestion: item.values[3].formattedValue,
              characterDetail: item.values[4].formattedValue,
              keyQuestion: item.values[5].formattedValue,
              keyDetails: item.values[6].formattedValue
            }

            cleanData.push(rowInfo)
          }
        });

        this.gSheet = cleanData

        // Sort cleanData into ordered and unordered decks
        cleanData.forEach((item) => {
          //if (item.ordered == "1") {
            this.orderedCards.push(item)
          /*} else if (item.ordered == "0") {
            this.unorderedCards.push(item)
          }*/
        });

      }).catch(error => {
        this.gSheet = [{text:'Error loading Google Sheet'}]
        console.log(error)
      })      
    }

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->

<style scoped>

  body {
    background: #50a958;  /* fallback for old browsers */
    background: -webkit-linear-gradient(to top, #50a958, #b1f1b7);  /* Chrome 10-25, Safari 5.1-6 */
    background: linear-gradient(to top, #50a958, #b1f1b7); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
    max-width: 600px;
    height: 100%;
    margin: auto;
    background-repeat: no-repeat;
    background-attachment: fixed;
    font-family: 'Arvo', serif;
  }

  .fade-enter-active {
    transition: opacity .5s;
  }
  .fade-enter /* .fade-leave-active below version 2.1.8 */ {
    opacity: 0;
  }

  .fade-leave-active {
    transition: opacity 0s;
  }
  .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
    opacity: 0;
  }

  li {
    list-style-type: disc;
    display: list-item;
    margin-left: 20px;
  }

  .card-body{
    min-height: 11em;
  }

  .btn-warning {
    color: #212529;
    background-color: #c5a55f;
    border-color: #422d00;
  }

  .btn-warning:focus,
  .btn-warning.focus {
    box-shadow: 0 0 0 .2rem rgba(86, 68, 29, 0.5)
  }

  .btn-warning:hover {
    background-color: #c39736;
    border-color: #422d00;
  }

  .monster{

    margin:auto;
    padding-top: 1em;
    padding-bottom: 1em;
  }

</style>
