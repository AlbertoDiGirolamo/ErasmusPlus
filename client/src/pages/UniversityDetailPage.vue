<template>
  <Header></Header>

  <Wallpaper :srcImg="srcImgWallpaper"></Wallpaper>

  <!-- Modal -->
  <div class="modal fade" id="applicationModal" tabindex="-1" aria-labelledby="myModal" aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header">
          <h1 class="modal-title fs-5" id="exampleModalLabel">Conferma candidatura</h1>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          Confermi la tua candidatura per l'università {{this.offerUniversity.name}}?
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-success" @click="applyToUniversity()" data-bs-dismiss="modal" >Conferma</button>
        </div>
      </div>
    </div>
  </div>

  <!-- Modal -->
  <div class="modal fade" id="deleteModal" tabindex="-1" aria-labelledby="myModal" aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header">
          <h1 class="modal-title fs-5" id="exampleModalLabel">Elimina offerta</h1>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          Confermi di voler eliminare l'offerta per l'università {{this.offerUniversity.name}}?
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-danger" @click="deleteUniversity();" data-bs-dismiss="modal" >Elimina</button>
        </div>
      </div>
    </div>
  </div>

  <div class="row me-5">
    <div class="col-md-1"></div>
    <div class="col-md-2 text-center" style="margin-top: -100px">
      <UniversityLogo :srcImg="srcImgUniversityLogo"></UniversityLogo>
    </div>
  </div>
  <div class="row me-5">
    <div class="col-md-1"></div>
    <div v-if="applicationsLoaded" class="col-md-2 mt-4 text-center">
      <button id="btnAccettata" v-if="checkIsApplied() === 'Accettata'" class="btn btn-success col-md-5" disabled>Accettata</button>

      <RouterLink class="nav-link col-md-4 mt-3 ms-5" :to="{path: '/survey/'+this.offerUniversity._id}">
        <button id="btnTermina" v-if="checkIsApplied() === 'Accettata'"  class="btn btn-outline-warning ps-3 pe-3" style="margin-left: 18px" @click="changeToTerminated()">Termina</button>
      </RouterLink>

      <button id="btnTerminata" v-if="checkIsApplied() === 'Terminata'" class="btn success" disabled>Terminata</button>

      <button id="btnRifiutata" v-if="checkIsApplied() === 'Rifiutata'" class="btn btn-danger" disabled>Rifiutata</button>
      <button id="btnAttesa" v-if="checkIsApplied() === 'Attesa'" class="btn btn-warning" disabled>Candidato</button>
      <button id="btnCandidati" v-else-if="this.user.role === 'Studente' && checkIsApplied() === '' && checkIsAccepted() == false" data-bs-toggle="modal" data-bs-target="#applicationModal" class="btn btn-outline-success">Candidati ora!</button>
      <button v-else-if="this.user.role === 'Admin' && someoneIsSigned == false" data-bs-toggle="modal" data-bs-target="#deleteModal" class="btn btn-outline-danger">Elimina</button>
      <button v-if="this.user.role === 'Admin' && someoneIsSigned == false" class="btn btn-outline-warning" style="margin-left: 50px" @click="modifyOffer()">Modifica</button>
      <label v-if="this.user.role === 'Admin' && someoneIsSigned == true" style="color: #D91A1A">Non puoi modificare quest'offerta, qualcuno è già iscritto!</label>
      <label id="threeOfferLabel" style="color: #D91A1A; visibility: hidden">Sei già candidato a 3 offerte!</label>
    </div>
    <div class="col-8"></div>
    <div v-if="this.user.role != 'Admin'" class="col-1 text-center" style="margin-top: -50px">
      <i id="heart" class="bi bi-heart heart-icon" @click="addToFavourites()"></i>
      <i id="heart-fill" class="bi bi-heart-fill" style="font-size: 2rem;color: #D91A1A; visibility:hidden; position: absolute; cursor: pointer" @click="removeFavourite()"></i>
    </div>
    <div class="col-3"></div>
    <div class="col-md-9 ">
      <div class="title">{{this.offerUniversity.name}}</div>
    </div>
  </div>
  <div class="row me-5">
    <div class="col-md-3"></div>
    <div class="col-md-9 ">
      <div class="place">{{this.offerUniversity.city}}, {{this.offerUniversity.country}}</div>
    </div>
  </div>
  <div class="row me-5">
    <div class="col-md-3"></div>
    <div class="col-md-9">
      <div class="plot">{{this.offerUniversity.plot}}</div>
    </div>
  </div>

  <div class="row" style="margin-top: 200px">
    <div class="col-md-9">
      <InfoPanelSX :title="msgApplicationsAvailable" :msgDescribe="msgDescribeApplicationsAvailable" :icon="iconApplicationsAvailable" :styleIcon="styleCSSIconMessage" :offerUniversity="msgInfoApplicationsAvailable" :key="offerUniversity.id" class="bgRedApplicationsAvailable" ></InfoPanelSX>
    </div>
    <div class="col-md-1">
      <CircleIcon class="circleIconApplicationsAvailable" :icon="iconApplicationsAvailable" :styleIcon="styleCSSIcon"></CircleIcon>
    </div>
    <div class="col-md-2"></div>
  </div>

  <div class="row" style="margin-top: 200px">
    <div class="col-md-2"></div>
    <div class="col-md-1">
      <CircleIcon class="circleIconExchangePeriod" :icon="iconExchangePeriod" :styleIcon="styleCSSIcon"></CircleIcon>
    </div>
    <div class="col-md-9">
      <InfoPanelDX :title="msgExchangePeriod" :msgDescribe="msgDescribeExchangePeriod" :icon="iconExchangePeriod" :styleIcon="styleCSSIconMessage" :offerUniversity="msgInfoExchangePeriod" :key="offerUniversity.id" class="bgGray"></InfoPanelDX>
    </div>
  </div>

  <div class="row" style="margin-top: 200px">
    <div class="col-md-9">
      <InfoPanelSX :title="msgFieldOfStudy" :msgDescribe="msgDescribeFieldOfStudy" :icon="iconFieldOfStudy" :styleIcon="styleCSSIconMessage" :offerUniversity="msgInfoFieldOfStudy" :key="offerUniversity.id" class="bgRedFieldOfStudy"></InfoPanelSX>
    </div>
    <div class="col-md-1">
      <CircleIcon class="circleIconFieldOfStudy" :icon="iconFieldOfStudy" :styleIcon="styleCSSIcon"></CircleIcon>
    </div>

  </div>
  <div class="row m-5"></div>
  <div v-if="this.statisticsComputed">
    <div class="row">
      <div class="col-1 me-5"></div>
      <div class="col-10 text-center mt-5 pt-5">
        <h1 class="stats-title pt-3 pb-3 me-5 pe-5">Statistiche</h1>
      </div>
    </div>

    <div class="row mt-5">
      <div class="col-md-1"></div>
      <div class="col-md-4 me-4">
        <PieChart :series="this.satisfaction" title="Soddisfazione Complessiva" width="500" v-if="this.statisticsComputed"></PieChart>
      </div>
      <div class="col-md-6 ms-3">
        <Histogram :data="this.teachingStatistics" color="#BB2E29" title="Didattica" :labels="didatticaLabels" width="700" v-if="this.statisticsComputed"></Histogram>
      </div>
    </div>
    <div class="row mt-5 mb-5 pb-5">
      <div class="col-md-1"></div>
      <div class="col-md-6 me-4">
        <BarChart :data="this.cityStatistics" title="Città" width="700" v-if="this.statisticsComputed"></BarChart>
      </div>
      <div class="col-md-4 ms-3">
        <Histogram :data="this.campusStatistics" color="#798897" title="Campus" :labels="campusLabels" v-if="this.statisticsComputed"></Histogram>
      </div>
    </div>
  </div>
</template>

<script>
import {defineComponent} from "vue";
import Header from "@/components/Header.vue";
import axios from "axios";
import InfoPanelSX from "../components/offer/InfoPanelSX.vue";
import CircleIcon from "../components/offer/CircleIcon.vue";
import Wallpaper from "../components/offer/Wallpaper.vue";
import UniversityLogo from "../components/offer/UniversityLogo.vue";
import InfoPanelDX from "../components/offer/InfoPanelDX.vue";
import Histogram from "@/components/offer/Histogram.vue";
import PieChart from "@/components/offer/PieChart.vue";
import BarChart from "@/components/offer/BarChart.vue";

export default defineComponent({
  name: "UniversityDetailPage",
  components: {BarChart, PieChart, Histogram, InfoPanelDX, UniversityLogo, Wallpaper, CircleIcon, Header, InfoPanelSX},
  data(){
    return{
      allApplications: [],
      applications: [],
      offerUniversity: [],
      title: [],
      msgApplicationsAvailable: "Posti disponibili: ",
      msgExchangePeriod: "Periodo di scambio: ",
      msgFieldOfStudy: "Ambito: ",
      msgDescribeApplicationsAvailable: "Questo dato indica i posti disponibili per l’offerta Erasmus. Le candidature verranno valutate dal responsabile di scambio e i risultati saranno disponabili appena le valutazioni saranno concluse.",
      msgDescribeExchangePeriod: "Questo dato indica il tempo massimo previsto per la durata dello scambio. A seguito della candidatura è possibile diminuire il tempo di permanenza e adattarlo alle proprie esigenze.",
      msgDescribeFieldOfStudy: "Questo dato indica il persorso di studi consigliato a cui fa riferimento la presente offerta. La coerenza dell'ambito con il proprio persorso di studio verrà valutata dal responsabile di scambio.",
      msgInfoApplicationsAvailable: "",
      msgInfoExchangePeriod: "",
      msgInfoFieldOfStudy: "",
      bgRed: [],
      icon: [],
      styleIcon: [],
      msgDescribe: [],
      offer: [],
      places: [],
      iconApplicationsAvailable : "bi bi-people msg-icon",
      iconExchangePeriod : "bi bi-calendar-week",
      iconFieldOfStudy : "bi bi-diagram-3",
      styleCSSIconMessage: "font-size: 2.5rem; color: #FFFFFF;",
      styleCSSIcon: "font-size: 2.5rem; color: #FFFFFF; background-color: #D9D9D9; border-radius: 50%; width: 100px; height: 100px; text-align: center; padding-top: 20px;",
      srcImgWallpaper: "",
      srcImgUniversityLogo: "",
      id: "",
      campusStatistics: [0,0,0,0],
      cityStatistics: [0,0,0,0],
      teachingStatistics: [0,0,0,0],
      apartmentsStatistics: [0,0,0,0],
      satisfaction: [0,0,0,0],
      statisticsComputed: false,
      user: [],
      isApplied: false,
      someoneIsSigned: false,
      applicationsLoaded: false,
      campusLabels: ["Servizi offerti", "Aule", "Laboratori", "Spazi aperti"],
      didatticaLabels: ["Corsi pertinenti", "Orari delle lezioni", "Tutor", "Disponibilità dei docenti"]
    }
  },
  methods:{

    getApplication(){
      axios.get('http://localhost:3000/applications').then(response =>{
        let allApplications;
        allApplications = response.data;
        allApplications.forEach(application => {
          if(application.student === (this.user.name+" "+this.user.surname)) {
            this.applications.push(application);
          }
        })
        this.checkIsApplied();
      }).catch(err => {
        console.log(err);
      })
    },
    computeStatistics(){
      for(let i = 0; i < this.offerUniversity.reviews.length; i++){
        let k = 0; //Index for inserting in the final arrays
        let sum = 0; //For calculating the satisfaction
        for (let j = 0; j < this.offerUniversity.reviews[i].length; j++){
          sum += this.offerUniversity.reviews[i][j];
          if(j < 4){
            this.campusStatistics[k] += this.offerUniversity.reviews[i][j]; //Sum of the statistics of the campus
          }else if(j >= 4 && j < 8){
            if(j === 4){
              k = 0;
            }
            this.cityStatistics[k] += this.offerUniversity.reviews[i][j]; //Sum of the statistics of the city
          }else if(j >= 8 && j < 12){
            if(j === 8){
              k = 0;
            }
            this.teachingStatistics[k] += this.offerUniversity.reviews[i][j]; //Sum of the statistics of the teaching
          }else{
            if(j === 12){
              k = 0;
            }
            this.apartmentsStatistics[k] += this.offerUniversity.reviews[i][j]; //Sum of the statistics of the apartments
          }
          k++;
        }

        let satisfaction = sum / 16;
        if(satisfaction <= 2){
          this.satisfaction[0]++;
        }else if(satisfaction > 2 && satisfaction <= 3){
          this.satisfaction[1]++;
        }else if(satisfaction > 3 && satisfaction <= 4){
          this.satisfaction[2]++;
        }else if(satisfaction > 4) {
          this.satisfaction[3]++;
        }
      }

      //Compute the average
      for(let k = 0; k < this.campusStatistics.length; k++){
        this.campusStatistics[k] = Math.round((this.campusStatistics[k] / this.offerUniversity.reviews.length)*10)/10;
        this.cityStatistics[k] = Math.round((this.cityStatistics[k] / this.offerUniversity.reviews.length)*10)/10;
        this.teachingStatistics[k] = Math.round((this.teachingStatistics[k] / this.offerUniversity.reviews.length)*10)/10;
        this.apartmentsStatistics[k] = Math.round((this.apartmentsStatistics[k] / this.offerUniversity.reviews.length)*10)/10;
      }
    },
    getUniversityDetail(id) {
      axios.get('http://localhost:3000/universitydetail' + id).then(response => {
            this.offerUniversity = response.data;
            this.msgInfoApplicationsAvailable = this.offerUniversity.offer.places;
            this.msgInfoExchangePeriod = this.offerUniversity.offer.period;
            this.msgInfoFieldOfStudy = this.offerUniversity.offer.field;
            this.srcImgWallpaper = this.offerUniversity.wallpaper;
            this.srcImgUniversityLogo = this.offerUniversity.logo;
            if(this.offerUniversity.reviews.length !== 0){
              this.computeStatistics();
              this.statisticsComputed = true;
            }
          }
      ).catch(err => {
        console.log(err);
      })
    },
    getUser(){
      axios.get('http://localhost:3000/userdetail'+ sessionStorage.getItem("mail")).then(response =>{
        this.user = response.data;
        for(let i=0; i<this.user.favourites.length; i++) {
          if(this.user.favourites[i].universityName == this.offerUniversity.name) {
            document.getElementById("heart").style.visibility = "hidden";
            document.getElementById("heart-fill").style.visibility = "visible";
          }
        }
        this.getApplication();

      }).catch(err => {
        console.log(err);
      })
    },
    deleteUniversity(){
      var uniName = this.offerUniversity.name;
      axios.delete('http://localhost:3000/deleteOffer'+this.offerUniversity.name).then(() => {

        axios.get('http://localhost:3000/users').then(response =>{
          this.users = response.data;
          const jsonNotifications = [];
          this.users.forEach(function(user){

            jsonNotifications.push({
              id: user.identificationNumber,
              text: "L'offerta delariva a " +uniName + " è stata eliminata!",
              read: "false",
              goto: "/"
            })
          });
          jsonNotifications.forEach(function (json){
            try{
              axios.post('http://localhost:3000/sendNotificationToUser', json, {
                headers: {
                  // Overwrite Axios's automatically set Content-Type
                  'Content-Type': 'application/json'
                }
              })
            } catch (e) {
              console.log(e)
            }
          })

        }).catch(err => {
          console.log(err);
        })
        this.$router.push('/');
      });
    },
    applyToUniversity(){
      this.getApplication();
      if(this.applications.at(2) == null) {
        const timeElapsed = Date.now();
        const today = new Date(timeElapsed);
        const json = {
          university:this.offerUniversity.name,
          city:this.offerUniversity.city,
          country:this.offerUniversity.country,
          student: this.user.name+ " " + this.user.surname,
          id_student: this.user.identificationNumber,
          date: today.toLocaleDateString(),
          state: "Attesa"
        };
        axios.post('http://localhost:3000/addApplication', json, {
          headers: {
            // Overwrite Axios's automatically set Content-Type
            'Content-Type': 'application/json'
          }
        }).then(() => location.reload());
      } else {
        document.getElementById("threeOfferLabel").style.visibility = "visible";
      }
      this.checkIsApplied();
      this.sendNotification();
    },
    checkIsApplied(){
      for(let i=0; i<this.applications.length; i++) {
        if(this.applications[i].university === this.offerUniversity.name) {
          return this.applications[i].state;
        }
      }
      return "";
    },
    checkIsAccepted(){
      for(let i=0; i<this.applications.length; i++) {
        if(this.applications[i].state === "Accettata" || this.applications[i].state === "Terminata") {
          return true
        }
      }
      return false;
    },
    addToFavourites(){
      const json = {
        universityName: this.offerUniversity.name
      };

      axios.post('http://localhost:3000/addFavourite'+sessionStorage.getItem("mail"), json,{
        headers: {
          // Overwrite Axios's automatically set Content-Type
          'Content-Type': 'application/json'
        }
      });
      document.getElementById("heart").style.visibility = "hidden";
      document.getElementById("heart-fill").style.visibility = "visible";

    },
    removeFavourite() {
      try{
        axios.post('http://localhost:3000/removeFavourite'+sessionStorage.getItem("mail"), this.offerUniversity, {
          headers: {
            // Overwrite Axios's automatically set Content-Type
            'Content-Type': 'application/json'
          }
        });
      } catch (e) {
        console.log(e)
      }

      document.getElementById("heart").style.visibility = "visible";
      document.getElementById("heart-fill").style.visibility = "hidden";

    },
    modifyOffer(){
      window.location.replace("/updateOffer/" + this.id);
    },
    sendNotification(){
      var json ={
        text: "L'utente "+ this.user.name+ " " + this.user.surname + " con numero di matricola: " + this.user.identificationNumber+", si è candidato all'offerta relativa a "+this.offerUniversity.name,
        read: "false",
        goto: "/applications"
      }
      try{
        axios.post('http://localhost:3000/sendNotificationToAdmin', json, {
          headers: {
            // Overwrite Axios's automatically set Content-Type
            'Content-Type': 'application/json'
          }
        })
      } catch (e) {
        console.log(e)
      }

      let jsonNotification = {
        id: sessionStorage.getItem("idUser"),
        text: "Ti sei candidato per l'offerta relativa a " + this.offerUniversity.name + "!",
        read: "false",
        goto: "/userdetail"
      };
      try{
        axios.post('http://localhost:3000/sendNotificationToUser', jsonNotification, {
          headers: {
            // Overwrite Axios's automatically set Content-Type
            'Content-Type': 'application/json'
          }
        })
      } catch (e) {
        console.log(e)
      }
    },
    getAllApplications(){
      axios.get('http://localhost:3000/applications').then(response =>{
            this.allApplications = response.data;
            this.checkIfSomeoneIsSigned();
            this.applicationsLoaded = true;
      }).catch(err => {
        console.log(err);
      })
    },
    checkIfSomeoneIsSigned(){
      this.allApplications.forEach(application => {
        if(application.university == this.offerUniversity.name) {
          if(application.state === 'Attesa' || application.state === 'Accettata'){
            console.log("Entrato")
            this.someoneIsSigned = true;
          }
        }
      });
    },
    changeToTerminated(){

      console.log("click Termina");
      const json = {
        universityName: this.offerUniversity.name
      };

      axios.post('http://localhost:3000/changeToTerminated'+sessionStorage.getItem("idUser"), json,{
        headers: {
          // Overwrite Axios's automatically set Content-Type
          'Content-Type': 'application/json'
        }
      });
    }
  },
  mounted() {
    this.id = this.$route.params.id;
    this.getUniversityDetail(this.id);
    this.getUser();
    this.getAllApplications();
    this.checkIsApplied();
  }
})
</script>

<style scoped>

.title{
  font-weight: bold;
  font-size: 55px;
}

.place{
  font-size: 35px;
}

.plot{
  font-size: 25px;
  margin-top: 100px;
}

.bgRedApplicationsAvailable{
  background-color: #BB2E29;
  color: white;
  padding-bottom: 60px;

}
.circleIconApplicationsAvailable{
  float: right;
}

.bgGray{
  background-color: #717171;
  float: right;
  color: white;
  padding-bottom: 60px;

}
.circleIconExchangePeriod{
  float: left;
}

.bgRedFieldOfStudy{
  background-color: #BB2E29;
  color: white;
}
.circleIconFieldOfStudy{
  float: right;
}
.stats-title{
  background-color: #FFFFFF;
  box-shadow: 0 5px 10px rgba(0,0,0,.2);
}

.heart-icon{
  font-size: 2rem;
  color: #D91A1A;
  visibility: visible;
  position: absolute;
  cursor: pointer
}
</style>