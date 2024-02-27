<template>
  <FilterMenu v-if="this.userAuthenticated && this.loggedUser.role !== 'Admin'" @filterClicked="filterClicked"></FilterMenu>
  <GoogleMap api-key="" class="map" :center="center" :zoom="5">
    <template v-for="university in this.universities">
      <CustomMarker v-if="this.checkUniversity(university)" :options="{ position: {lat: university.latitude, lng: university.longitude}, anchorPoint: 'BOTTOM_CENTER' }">
        <RouterLink class="nav-link" :to="{path: '/universitydetail/'+university._id}">
          <div style="text-align: center" class="marker">
            <!--<img :src="getImageUrl(university.logo)" width="50" height="50" /> -->
            <img :src="getImageUrl(university.cardImg)" width="70" height="70" style="border-radius: 10%; margin-bottom: 20px; object-fit: cover;" />
          </div>
        </RouterLink>
      </CustomMarker>
    </template>
  </GoogleMap>
</template>

<script>
import {defineComponent} from "vue";
import FilterMenu from "@/components/home/FilterMenu.vue";
import {GoogleMap, Marker, CustomMarker} from "vue3-google-map";
import UniversityCard from "@/components/home/UniversityCard.vue";
import axios from "axios";
export default defineComponent({
  name: "MapContainer",
  components: {UniversityCard, FilterMenu, GoogleMap, Marker, CustomMarker},
  props: ["universities"],
  emits: ["toList"],
  data(){
    return{
      loggedUser: null,
      universityIsOk: true,
      studyFilter: false,
      halfFilter: false,
      fullFilter: false,
      favouritesFilter: false,
      userAuthenticated: false
    }
  },
  setup() {
    const center = { lat: 45.3850639, lng: 12.1734035};

    return { center };
  },
  methods:{
    switchToList(){
      this.$emit('toList', false);
    },
    getLoggedUser(){
      axios.get('http://localhost:3000/userdetail'+ sessionStorage.getItem("mail")).then(response =>{
        this.loggedUser = response.data;
        this.userAuthenticated = true;
      }).catch(err => {
        console.log(err);
      })
    },
    filterClicked(filter){
      if(filter === "half") {
        this.halfFilter = !this.halfFilter;
        this.fullFilter = false;
      } else if(filter === "full"){
        this.fullFilter = !this.fullFilter;
        this.halfFilter = false;
      }else if(filter === "study"){
        this.studyFilter = !this.studyFilter;
      }else if(filter === "favourites"){
        this.favouritesFilter = !this.favouritesFilter;
      }
    },
    checkUniversity(university){
      if(this.halfFilter && university.offer.period !== "6"){
        return false;
      }
      if(this.fullFilter && university.offer.period !== "12"){
        return false;
      }
      if(this.studyFilter && this.loggedUser.course !== university.offer.field){
        return false;
      }

      if(this.favouritesFilter){
        for(let i = 0; i < this.loggedUser.favourites.length; i++){
          if (this.loggedUser.favourites[i].universityName === university.name){
            return true;
          }
        }
        return false;
      }

      return true;
    },
    getImageUrl(srcImg){
      return new URL(`${srcImg}`, import.meta.url)
    }
  },
  mounted() {
    this.getLoggedUser()
  }
});
</script>

<style scoped>
.map{
  width: 100%;
  height: 88vh;
}
.marker:hover{
  transform: scale(1.20);
}
</style>
