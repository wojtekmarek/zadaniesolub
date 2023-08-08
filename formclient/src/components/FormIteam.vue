<script>
import "../assets/form.css";
export default {





    data() {
        return {
            message: '',
            name: '',
            lastname: '',
            email: '',
            unvalidmessage: false,
            unvalidname: false,
            unvalidlastname: false,
            unvalidemail: false,
            showtable: false,
            errortextare: "Pole musi być uzupełnione"



        };
    },

    methods: {
        validateall() {

            if (!this.unvalidmessage && !this.unvalidname && !this.unvalidlastname && !this.unvalidemail) { return true; } else {
                this.validInput(document.getElementById("name"));
                this.validInput(document.getElementById("lastname"));
                this.validInput(document.getElementById("email"));
                this.validInput(document.getElementById("message"));
                return false;
            }
        },

        hideNotificationError(name) {

            this['unvalid' + name] = false;
        },
        shownNotificationError(name) {

            this['unvalid' + name] = true;
        },
        checkreg(name, value, regs, len) {

            if (regs.test(value) && value.length <= len) {
                this.hideNotificationError(name);
                this[name] = value;
                return true;
            } else {
                this.shownNotificationError(name);
                return false;

            }
        }
        ,
        validInput(event) {

            //console.log(this['unvalid' + event.id]);
            // console.log(event);
            if (event.id == "email") {
                var regemail = new RegExp(/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}$/i);
                this.checkreg(event.id, event.value, regemail, 100);
            } else if (event.id == "message") {
                var reg = new RegExp(/^[a-zA-Z żźćńółęąśŻŹĆĄŚĘŁÓŃ]+$/i);
                console.log(event.value.length);
                if (event.value >= 65535) {
                    this.errortextare = "Pole musi być uzupełnione";
                    this.checkreg(event.id, event.value, reg, 65535);
                } else {
                    this.errortextare = "Wiadomość przekroczyła dopuszczalną liczbe znaków";
                }

            } else {
                var reg = new RegExp(/^[a-zA-Z żźćńółęąśŻŹĆĄŚĘŁÓŃ]+$/i);
                this.checkreg(event.id, event.value, reg, 255);
            }



        },
        async send() {
            let body = {
                "name": this.name,
                "lastname": this.lastname,
                "email": this.email,
                "message": this.message,
            }
            console.log(typeof body)

            if (this.validateall()) {
                fetch('http://127.0.0.1:8000/add', {
                    method: "POST",
                    mode: "cors",
                    cache: "no-cache",
                    headers: {
                        'Content-Type': 'text/plain',



                    },
                    body: JSON.stringify(body)//'{\r\n"name":"Wojciech",\r\n"lastname":"Marek",\r\n"email":"wojtekmarek@gmail.com",\r\n"message":"Lorem ipsk sjacfb asodm3 ad onadcexioed ioahncn e ni dewqn qenidn idqwe ni"\r\n\r\n}'//body,

                }).then(res => {

                    console.log(res);


                    if (res.status === 200) {
                        this.showtable = true;
                        console.log(this.showtable);
                    } else {
                        console.log("Zapis do bazy się nie powiódł")
                    }
                })
                    .catch(() => {
                        //handle any errors here
                    });

            } else {
                alert("Please compleate text to translate.");
            }

        }
    }
}
</script>
<template>
    <div class="form_div">
        <h1>Form test</h1>
        <label for="name">Imię</label>
        <input class="inputfield" type="text" name="name" id="name" v-model="name" v-on:blur="validInput($event.target)" />
        <div class="errordiv" v-if="this.unvalidname">Pole musi być uzupełnione</div>
        <label for="lastname">Nazwisko
        </label>
        <input class="inputfield" type="text" name="lastname" id="lastname" v-model="lastname"
            v-on:blur="validInput($event.target)" />
        <div class="errordiv" v-if="this.unvalidlastname">Pole musi być uzupełnione</div>
        <label for="email">Email
        </label>
        <input class="inputfield" type="text" name="email" id="email" v-model="email"
            v-on:blur="validInput($event.target)" />
        <div class="errordiv" v-if="this.unvalidemail"> Niepoprawny adres email</div>
        <label class="form_textarea_label" for="message">Wiadomość</label>
        <textarea class="inputfield form_textarea " v-model="message" id="message" name="message"
            help="Please write text to translete." v-on:blur="validInput($event.target)"></textarea>
        <div class="errordiv" v-if="this.unvalidmessage">{{this.errortextare}}</div>
        <button class="translate_button" @click="send(this)">
            Wyślij
        </button>
        <div v-if="this.showtable" class="table">

            <div class="tablecelname">Imie:</div>
            <div class="tablecel">{{ this.name }} </div>

            <div class="tablecelname">Nazwisko</div>
            <div class="tablecel">{{ this.lastname }} </div>

            <div class="tablecelname">Email</div>
            <div class="tablecel">{{ this.email }} </div>



            <div class="tablecelname">Wiadomość:</div>
            <div class="tablecel">{{ this.message }} </div>


        </div>
    </div>
</template>