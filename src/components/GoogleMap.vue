<template>
  <div>
 
        <div style="margin-top:50px;margin-left:500px" class="row">
        

<div class="col-sm1"> 
  <h6 style="margin-top:10px">กรุณาเลือก Options</h6>

  </div>
<div class="col-sm1"> 
  <h1 style="margin:0px;margin-left:20px"><b-form-select  v-model="selected" :options="options" class="mb-2" /></h1>
  </div>
  <v-btn style="margin-top:0px;color:black;margin-left:20px" v-on:click="addMarker" color="success">ค้นหา</v-btn>

</div>

      <div style="margin-left:15%;margin-top:30px">
    <gmap-map
      :center="center"
      :zoom="12"
      style="width:80%;  height: 500px; margin-left:10%:margin-top:0px"
    >
      <gmap-marker
        :key="index"
        v-for="(m, index) in markers"
        :position="m.position"
        @click="center=m.position"
      ></gmap-marker>
    </gmap-map>
      </div>
  </div>
</template>

<script>
import firebase from 'firebase'
import {db} from '../firebase'
let booksRef = db.ref('Location');
let rateRef = db.ref('Places');
let bookReff=db.ref('books');
var childCount;
var childValue=0;
var childRate=0;
var sumRate=0;
var childNum=0;
var childAverage=0;
var placeName;
var DateStamp='Test';
export default {
  name: "GoogleMap",
  firebase: {
    posts: booksRef
  },
  data() {
    return {
      selected:1,
      options:[
        { value: 1, text: 'จากผู้ใช้ให้คะแนน' },
        { value: 2, text: 'จากการติดตามผู้ใช้' }
      ],
      // default to Montreal to keep it simple
      // change this to whatever makes sense
      center: { lat: 6.7512336, lng: 101.091477 },
      markers: [
      /*  {
        position:{
        lat: 7.0071889, lng: 100.4962275
      }
        }*/ ],
      places: [],
      currentPlace: null
    };
  },

  mounted() {
    this.geolocate();
  },

  methods: {
    // receives a place object via the autocomplete component
    setPlace(place) {
      this.currentPlace = place;
    },
    addMarker() {   
      var i=0;
      var returnArray=[];
      var returnArray2=[];
  
       
      var marker=[];
      var marker2=[];
  
     /*  const marker = {
          lat:parseFloat(posts.latt),
          lng: parseFloat(posts.long)
        };
        console.log(posts.latt)
        this.markers.push({ position: marker });*/
    var dbRef= firebase.database().ref('Location');
    var dbRefUsers = firebase.database().ref('Location(users)');
    
    if(this.selected==2){
      marker=[];
    dbRef.on('value', function(snapshot) {
    snapshot.forEach(function(child) {  
    var childs=child.val();
     
   var childs1=childs.latt
   var childs2=childs.long
   returnArray.push(childs1)
   returnArray2.push(childs2)
          console.log(returnArray)
          console.log(returnArray2)
         
       });
       
      return returnArray;
      return returnArray2
   });
    
     // if (this.currentPlace) {
    //  console.log(lat1)
     // console.log(lat2)
   // booksRef.on('value', function(snapshot) {
   // snapshot.forEach(function(child) {  
   // var childs=child.val();
   for(i=0;i<returnArray.length;i++){
     
     
       marker[i] = {
          lat:returnArray[i],
          lng: returnArray2[i]
        };
        this.markers.push({ position: marker[i] });
        /*this.places.push(this.currentPlace);
        this.center = marker;
        this.currentPlace = null;*/
   }
   
     //  });
   //});
      /*
        const marker2 = {
          lat:7.0428779,
          lng: 100.4962275
        };
           this.markers.push({ position: marker2 });
        this.places.push(this.currentPlace);
        this.center = marker2;
        this.currentPlace = null;*/
    //  }
    } if(this.selected==1){
      marker=[];
    dbRefUsers.on('value', function(snapshot) {
    snapshot.forEach(function(child) {  
    var childs=child.val();
   var childs1=childs.latt
   var childs2=childs.long
   returnArray.push(childs1)
   returnArray2.push(childs2)
          console.log(returnArray)
          console.log(returnArray2)
       });
      return returnArray;
      return returnArray2
   });
    
     // if (this.currentPlace) {
    //  console.log(lat1)
     // console.log(lat2)
   // booksRef.on('value', function(snapshot) {
   // snapshot.forEach(function(child) {  
   // var childs=child.val();
   for(i=0;i<returnArray.length;i++){
     
     
       marker2[i] = {
          lat:returnArray[i],
          lng: returnArray2[i]
        };
        this.markers.push({ position: marker2[i] });
        /*this.places.push(this.currentPlace);
        this.center = marker;
        this.currentPlace = null;*/
   }
  
    }
    }
    
,
    addMarker2() {
      var CountTest =firebase.database().ref('Places');
       
    CountTest.on('value', function(snapshot) {
    var count=0;
    snapshot.forEach(function(child) {

   var childs=child;
   var childs2=child.val();
     count=child.numChildren();
   console.log(count)
   
     
  
       });
      //  console.log(count);
   });
  
    },


    calculate(){
        

        rateRef.once("value",function(snapshot){
        
          snapshot.forEach(function(childs){
            let   booksReff=db.ref('books');
            childs.forEach(function(childss){
                  childCount= childss.numChildren();
                  sumRate=sumRate+childss.val().Rate;
                  DateStamp=childss.val().Date;
                 
                  
            })
         
            console.log(sumRate);
            childNum= childs.numChildren();
            childAverage=sumRate/childNum;
            console.log(childNum);
             placeName=childs.key;
            console.log(placeName);
           booksReff.child(placeName).set({
             name:placeName,
             rate:childAverage,
             timestamp:DateStamp
        
           })
           console.log(DateStamp);
           //booksReff.push('sad');
            console.log(childAverage);
            // rateRef.push(childAverage);
            sumRate=0;
             childAverage=0;
           
          })
       
        })
        
    },
    geolocate: function() {
      navigator.geolocation.getCurrentPosition(position => {
        this.center = {
          lat: position.coords.latitude,
          lng: position.coords.longitude
        };
      });
    }
  }
};
</script>