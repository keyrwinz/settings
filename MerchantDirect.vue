<template>
  <div class="merchant-holder" v-if="data !== null">
    <span class="header">{{params}} Settings</span>
    <span class="content">
      <span class="error text-danger" v-if="errorMessage !== null">
        <b>Oops!</b> {{errorMessage}}
      </span>
      <!-- <span class="error text-success" v-if="successMessage !== null">
        {{successMessage}}
      </span> -->
      <br>
      <span class="inputs" >
        <div class="form-group">  
          <label for="address">{{params}} name <label class="text-danger">*</label></label>
          <input type="text"  :readonly="isDisabled" class="form-control" :placeholder="params + ' name'" v-model="data.name" :disabled="parseInt(data.account_id) !== parseInt(user.userID)" >
        </div>

        <div class="form-group" style="margin-top: 25px;">
          <label for="address">{{params}} e-mail address<label class="text-danger">*</label></label>
          <input type="text"  :readonly="isDisabled" class="form-control" :placeholder="params + ' e-mail address'" v-model="data.email" :disabled="parseInt(data.account_id) !== parseInt(user.userID)">
        </div>

        <div class="form-group" style="margin-top: 25px;">
          <label for="address">{{params}} address <label class="text-danger">*</label></label>
          <input type="text" :readonly="isDisabled" class="form-control" :placeholder="params + ' address'" v-model="data.address" :disabled="parseInt(data.account_id) !== parseInt(user.userID)">
        </div>

        <div class="form-group" style="margin-top: 25px;">
          <label for="address">{{params}} Sanition Schedule <label class="text-danger">*</label></label>
          <input type="text" class="form-control" :placeholder="params + ' Sanition Schedule'" v-model="newData.schedule" disabled>
          <br/>
          Select Schedule from Available days below:<br/>
          <div class="row"> 
            <div class="col-sm-2" v-for="(day, index) in days" :key="index">
              <button class="btn btn-light" @click="selectDay(day)">{{day}}</button>
            </div>
          </div>
        </div>

<!--   <div class="form-group" style="margin-top: 25px;" v-if="allowed.indexOf('prefix') > -1">
          <label for="address">Prefix</label>
          <input type="text" class="form-control" placeholder="Invoice Prefix eq. IDF" v-model="data.prefix" :disabled="parseInt(data.account_id) !== parseInt(user.userID)">
        </div>

        <div class="form-group" style="margin-top: 25px;" v-if="allowed.indexOf('website') > -1">
          <label for="address">Website</label>
          <input type="text" class="form-control" placeholder="Company website url" v-model="data.website" :disabled="parseInt(data.account_id) !== parseInt(user.userID)">
        </div> -->
        
        <!-- <button class="btn btn-primary" style="margin-bottom: 25px;" @click="update()" :disabled="isDisabled" v-if="parseInt(data.account_id) === parseInt(user.userID)">Update</button> -->
        
        <button v-bind:class="[ isDisabled ? 'btn btn-primary' : 'btn btn-success' ]" style="margin-bottom: 25px;" @click="isDisabled ? editProfile() : update()">{{ isDisabled ? 'Edit Profile' : 'Update Profile' }}</button>
        <button class="btn btn-secondary" style="margin-bottom: 25px;" @click="cancelEdit" v-if="!isDisabled">Cancel</button>
      
      </span>
      <span class="sidebar" v-if="createFlag === false">
        <span class="sidebar-header" style="margin-top: 25px;">{{params}} Logo</span>
        <span class="image" v-if="data.logo !== null">
          <img :src="config.BACKEND_URL + data.logo" height="auto" width="100%" >
        </span>
        <span class="image" v-else>
          <i class="fa fa-image" ></i>
        </span>
        <span style="width: 100%; float: left; text-align: center;">
          <label v-if="data.status === 'not_verified'" class="text-grey"><i>Not verified</i></label>
          <label v-if="data.status === 'verified'" class="text-primary"><i>Verified</i></label>
        </span>
        <button class="btn btn-primary custom-block" style="margin-top: 5px;" @click="showImages()" v-if="parseInt(data.account_id) === parseInt(user.userID)">Select from images
        </button>
      </span>
    </span>
    <browse-images-modal :object="photoObject"></browse-images-modal>

  <!-- MODAL FOR UPDATE NOTIFICATION-->
    <div class="modal fade right" id="successfully-updated" tabindex="1" role="dialog" aria-labelledby="myModalLabel"
     aria-hidden="true">
      <div class="modal-dialog modal-side modal-notify modal-primary " role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" v-bind:style="[ successfulUpdate ? {'color': 'green'} : {'color': 'red'} ]" >{{successfulUpdate ? successMessage : 'Failed to update!'}}</h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true" class="white-text">&times;</span>
            </button>
          </div>
          <div class="modal-body p-4" >
            <p >{{successfulUpdate ? 'Your profile is successfully updated.' : 'Please do any changes of your profile.'}}</p>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-primary" data-dismiss="modal">Okay</button>
            </div>
        </div>
      </div>
    </div>


  </div>
  
</template>
<style scoped>
.merchant-holder{
  width: 95%;
  float: left;
  margin-left: 5%;
  margin-bottom: 200px;
}
.header{
  width: 100%;
  height: 50px;
  line-height: 50px;
  font-size: 24px;
  border-bottom: solid 1px #ddd;
  float: left;
}
.content{
  width: 100%;
  min-height: 50px;
  float: left;
  overflow-y: hidden;
}
.content .error{
  width: 100%;
  float: left;
  height: 40px;
  line-height: 40px;
}
.inputs{
  width: 65%;
  float: left;
  margin-right: 5%;
  min-height: 50px;
  overflow-y: hidden;
}
.sidebar{
  width: 30%;
  float: left;
  min-height: 50px;
  overflow-y: hidden;
}
.sidebar-header{
  height: 40px;
  line-height: 40px;
  width: 100%;
  float: left;
}
.sidebar .image{
  width: 100%;
  float: left;
  min-height: 200px;
  overflow-y: hidden;
  text-align: center;
}
.image i{
  font-size: 150px;
  line-height: 200px;
}
.image img{
  border-radius: 5px;
}
.custom-block{
  width: 100%;
  float: left;
}
.custom-block input{
  display: none;
}
.row{
  margin: 0px !important;
}
.row .left{
  padding-left: 0px !important;
}
.row .right{
  padding-right: 0px !important;
}
@media screen and (max-width: 992px){
  .holder{
    width: 96%;
    margin-left: 2%;
    margin-right: 2%;
  }
  .sidebar, .inputs{
    width: 100%;
    margin-right: 0%;
    margin-left: 0%;
  }
  .row .form-group{
    padding-left: 0px !important;
    padding-right: 0px !important;
  }
}
</style>
<script>
import ROUTER from 'src/router'
import AUTH from 'src/services/auth'
import axios from 'axios'
import CONFIG from 'src/config.js'
export default {
  mounted(){
    if(this.user.type !== 'ADMIN' && this.user.type !== 'BUSINESS' && this.user.type !== 'AGENCY_GOV' && this.user.type !== 'AGENCY_BRGY'){
      ROUTER.push('/dashboard')
    }
    $('#loading').css({display: 'block'})
    this.retrieve()
    if(this.$route.path.includes('barangay')){
      this.params = 'Barangay'
    }else if(this.$route.path.includes('business')){
      this.params = 'Business'
    }else if(this.$route.path.includes('lgu')){
      this.params = 'LGU'
    }
    console.log(this.beforeEditValues)
  },
  data(){
    return {
      beforeEditValues: {
        name: null,
        email: null,
        address: null
      },
      successfulUpdate: false,
      isDisabled: true,
      edit: false,
      user: AUTH.user,
      tokenData: AUTH.tokenData,
      config: CONFIG,
      data: null,
      errorMessage: null,
      successMessage: null,
      newData: {
        account_id: AUTH.user.userID,
        prefix: null,
        logo: null,
        email: null,
        address: null,
        name: null,
        website: null,
        schedule: []
      },
      createFlag: false,
      photoObject: {
        url: null
      },
      params: 'Business',
      days: ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
    }
  },
  components: {
    'browse-images-modal': require('components/increment/generic/image/BrowseModal.vue')
  },
  methods: {
    selectDay(day){
      if(this.newData.schedule.includes(day)){
        // console.log(this.data.schedule)
        this.newData.schedule.splice(this.newData.schedule.indexOf(day), 1)
      }else{
        this.newData.schedule.push(day)
        // console.log(this.newData.schedule)
      }
    },
    cancelEdit(){
      this.isDisabled = true
      this.errorMessage = null
      this.retrieve()
    },
    editProfile(){
      this.isDisabled = false
    },
    retrieve(){
      let parameter = {
        condition: [{
          value: this.user.userID,
          column: 'account_id',
          clause: '='
        }]
      }
      this.APIRequest('merchants/retrieve', parameter).then(response => {
        $('#loading').css({display: 'none'})
        if(response.data.length > 0){
          if(response.data[0].schedule !== null){
            let tempRes = response.data[0].schedule.replace(/,/g, ' ')
            let Res = tempRes.trim().split(' ')
            Res.forEach(doc => {
              this.newData.schedule.push(doc)
            })
          }
          this.data = response.data[0]
          this.beforeEditValues.name = response.data[0].name
          this.beforeEditValues.email = response.data[0].email
          this.beforeEditValues.address = response.data[0].address
          this.createFlag = false
        }else{
          this.createFlag = true
          this.data = this.newData
        }
      })
    },
    update(url){
      if(this.createFlag === true){
        this.create()
        return
      }
      if(this.beforeEditValues.name === this.data.name && this.beforeEditValues.email === this.data.email && this.beforeEditValues.address === this.data.address){
        this.successfulUpdate = false
        $('#successfully-updated').modal('show')
      }else if((this.data.name === '') || (this.data.email === '') || (this.data.address === '')){
        this.successfulUpdate = false
        $('#successfully-updated').modal('show')
      }else{
        this.successfulUpdate = true
        if(this.data.email !== null && AUTH.validateEmail(this.data.email) === false){
          this.errorMessage = 'Invalid email address.'
          return
        }
        if(this.createFlag === false){
          this.data.logo = url
          this.data.schedule = this.newData.schedule.toString()
          $('#loading').css({display: 'block'})
          this.APIRequest('merchants/update', this.data).then(response => {
            if(response.data === true){
              this.retrieve()
              this.successMessage = 'Successfully Updated!'
              this.errorMessage = null
            }else{
              this.successMessage = null
              this.errorMessage = 'Unable to Update! Please contact the administrator.'
            }
            this.newData.schedule = []
          })
        }else{
          this.create(url)
        }
        this.isDisabled = true
        setTimeout(() => {
          $('#successfully-updated').modal('show')
        }, 1000)
      }
    },
    create(url){
      this.data.logo = url
      if(this.data.email !== null && AUTH.validateEmail(this.data.email) === false){
        this.errorMessage = 'Invalid email address.'
        return
      }
      this.APIRequest('merchants/create', this.data).then(response => {
        if(response.data > 0){
          this.retrieve()
          this.successMessage = 'Successfully Updated!'
          this.errorMessage = null
          AUTH.checkAuthentication(null, true)
        }else{
          this.successMessage = null
          this.errorMessage = 'Unable to Update! Please contact the administrator.'
        }
      })
    },
    updatePhoto(object){
      this.data.logo = object.url
      this.update()
      this.hideImages()
    },
    createPhoto(object){
      this.data.logo = object.url
      this.update()
      this.hideImages()
    },
    showImages(){
      $('#browseImagesModal').modal('show')
    },
    manageImageUrl(url){
      this.update(url)
    }
  }
}
</script>
