<template>
  <div class="card-header">
    <h3>Register</h3>
  </div>
  <div class="card-body">
    <form id="register_form" ref="register_form" @submit.prevent="submitter">
      <label class="look"><span>Checker</span><input v-model="faker" type="text"></label>
      <label><span>Email</span><input v-model.lazy="email" required type="email"></label>
      <label><span>First Name</span><input v-model="name.first" required type="text"></label>
      <label><span>Last Name</span><input v-model="name.last" required type="text"></label>
      <label><span>Password</span><input v-model="password" autocomplete="off" required type="password"></label>
      <hr>

      <label><span>Phone</span><input v-model="phone" required type="text"></label>
      <label><span>Street Address</span><input v-model="address.street" required type="text"></label>
      <label><span>City</span><input v-model="address.city" required type="text"></label>
      <label><span>State</span><input v-model="address.state" required type="text"></label>
      <label><span>Country</span><input v-model="address.country" required type="text"></label>
      <hr>

      <label>
        <span>What will you be attending the event as?</span>
        <select id="" v-model="user_category" class="select-css" name="" required>
          <option value="audience">Audience</option>
          <option value="player">Player</option>
          <option value="volunteer">Volunteer</option>
        </select>
      </label>

      <label><span>How many guest will you have</span><input v-model.number="guests" max="5" min="0"
                                                             type="number"></label>

      <div v-if="guests>0" class="guests">
        <label v-for="i in guests" :key="i"><span>Guest {{ i }} Name</span><input v-model="guest_names[i-1]" required
                                                                                  type="text"></label>
      </div>
      <button ref="submit_btn" style="display:none;">Submit</button>
    </form>
  </div>
  <div class="card-footer">
    <span>Total Cost: {{ total_price }}</span>
    <button class="submit big_submit" @click="$refs.submit_btn.click()">Proceed to Payment</button>
    <button class="submit small_submit" @click="$refs.submit_btn.click()"><span class="material-icons">arrow_forward</span></button>
  </div>
</template>

<script>
export default {
  name: "RegistrationForm",
  props:['userData'],
  emits:{
    'go-to':null
  },
  data() {
    return {
      faker: '',
      email: this.$route.query.email,
      name: {
        first: '',
        last: ''
      },
      phone:'',
      address: {
        street:'',
        city:'',
        state:'',
        country:''
      },
      guest_names: [],
      password: '',
      guests: 0,
      user_category: '',
    }
  },
  watch:{
    email(val){
      this.$router.replace({query: {email:val}})
    },
    guests(val){
      if(val < 0 || val > 5){
        this.guests = 0
        this.guest_names = []
      }
    }
  },
  computed:{
    total_attendees(){
      if(this.guests <= 5 && this.guests >= 0){
        return (Math.floor(this.guests) + 1)
      }
      return 1
    },
    total_price(){
      if(this.user_category !== 'volunteer'){
        return "$"+this.total_attendees * 25
      } else {
        return "$"+this.guests * 25
      }
    }
  },
  methods:{
    submitter(){
      if(this.faker === ''  && this.validEmail(this.email)){
        if (this.email !== '' &&
          this.password !== '' &&
          this.name.first !== '' &&
          this.name.last !== ''){

          let desc = 'Ticket purchase for '+this.name.first+" "+this.name.last
          if (this.guests > 0){
            desc = desc + ' and '+this.guests+' guests.'
          }

          if(this.user_category === 'volunteer'){
            desc = desc + ' Volunteer discount included.'
          }

          let price = this.total_price.replace('$','')

          const data = {
            name:this.name,
            email:this.email,
            password:this.password,
            phone:this.phone,
            address: this.address,
            user_category:this.user_category,
            total_price:price,
            total_guests:this.guests,
            guests:this.guest_names,
            gotoPayment:true,
            description:desc
          }
          this.$emit('go-to',data)
        } else {
          console.log('Required fields missing')
        }
      } else {
        console.log('invalid email')
      }
    },
    validEmail: function (email) {
      const re = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      return re.test(email);
    }
  }
}
</script>

<style scoped>


.look{
  display: none;
}

form{
  display: flex;
  flex-direction: column;
}

button{
  background-color: var(--bg-frontEnd);
  border: none;
  outline: none;
  box-shadow: 0 0 10px rgba(0,0,0,.13);
}

button:active, button:focus{
  box-shadow: none;
  background-color: #D5D5D5;
}


.card-header{
  padding: 30px;
  display: grid;
  place-items: center;
  background-color: #f44336;
  color: white;
  border-radius: 25px 25px 0 0;
}

.card-body{
  width: 100%;
  padding: 20px 40px;
  /*margin-bottom: 40px;*/
  overflow-y: scroll;
}

form{
  display: flex;
  flex-direction: column;
  width: 100%;
}

label{
  display: flex;
  flex-direction: column;
  margin: 10px 0;
  width: 100%;
}

input{
  height: 40px;
  font-size: 16px;
  padding: 15px;
  margin: 10px 0;
  outline: none;
  border-radius: 5px;
  border: 1px solid #aaa;
}

.card-footer{
  padding: 15px 30px;
  background-color: #a9a9a9;
  color: white;
  border-radius: 0 0 25px 25px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.submit{
  padding: 15px;
  background-color: #f44336;
  border-radius: 10px;
  color: white;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.4);
}

.small_submit{
  display: none;
}

.select-css {
  display: block;
  font-size: 16px;
  font-family: sans-serif;

  color: #444;
  line-height: 1.3;
  padding: .6em 1.4em .5em .8em;
  width: 100%;
  max-width: 100%;
  box-sizing: border-box;
  margin: 10px 0;
  border: 1px solid #aaa;
  box-shadow: 0 1px 0 1px rgba(0,0,0,.04);
  border-radius: 5px;
  -moz-appearance: none;
  -webkit-appearance: none;
  appearance: none;
  background-color: #fff;
  background-image: url('data:image/svg+xml;charset=US-ASCII,%3Csvg%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%20width%3D%22292.4%22%20height%3D%22292.4%22%3E%3Cpath%20fill%3D%22%23007CB2%22%20d%3D%22M287%2069.4a17.6%2017.6%200%200%200-13-5.4H18.4c-5%200-9.3%201.8-12.9%205.4A17.6%2017.6%200%200%200%200%2082.2c0%205%201.8%209.3%205.4%2012.9l128%20127.9c3.6%203.6%207.8%205.4%2012.8%205.4s9.2-1.8%2012.8-5.4L287%2095c3.5-3.5%205.4-7.8%205.4-12.8%200-5-1.9-9.2-5.5-12.8z%22%2F%3E%3C%2Fsvg%3E');
  background-repeat: no-repeat, repeat;
  background-position: right .7em top 50%, 0 0;
  background-size: .65em auto, 100%;
}
.select-css::-ms-expand {
  display: none;
}
.select-css:hover {
  border-color: #888;
}
.select-css:focus {
  border-color: #aaa;
  box-shadow: 0 0 1px 3px rgba(59, 153, 252, .7);
  box-shadow: 0 0 0 3px -moz-mac-focusring;
  color: #222;
  outline: none;
}
.select-css option {
  font-weight:normal;
}

input:hover {
  border-color: #888;
}
input:focus {
  border-color: #aaa;
  box-shadow: 0 0 1px 3px rgba(59, 153, 252, .7);
  color: #222;
  outline: none;
}

.submit:active, .submit:focus{
  color: black;
}

@media only screen and (max-width: 700px){

  .small_submit{
    display: grid;
    place-items: center;
    height: 40px;
    padding: 0;
    width: 40px;
    border-radius: 10px;
  }
  .big_submit{
    display: none;
  }
  .card-header{
    padding: 20px;
    border-radius: 15px 15px 0 0;
  }

  .card-body{
    padding: 15px 25px;
  }

  input,select{
    height: 35px;
    font-size: 16px;
  }

  .card-footer{
    padding: 10px 30px;
  }


}

</style>
