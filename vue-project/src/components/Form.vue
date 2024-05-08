<script setup>
    import Text from './Text.vue';

    class FormSubmit{
        constructor(settings){
            this.settings = settings;
            this.form = document.querySelector(settings.form);
            this.formButton = document.querySelector(settings.button);

            if (this.form) {
                this.url = this.form.getAttribute("action");
            }

            this.sendForm = this.sendForm.bind(this)
        }

        displaySuccess(){
            this.form.innerHTML = this.settings.success;
        }

        displayError(){
            this.form.innerHTML = this.settings.error;
        }

        getFormObject(){
            const formObject = {}
            const fields = this.form.querySelectorAll("[name]");
            fields.forEach((field) => { 
                formObject[field.getAttribute("name")] = field.value;   
            });
            return formObject;
        }

        onSubmission(event){
            event.preventDefault();
            event.target.disable = true;
            event.target.innerText = "Enviando..."
        }

        async sendForm(event){
        try{
            this.onSubmission(event);
            await fetch(this.url, {
                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                    Accept: "application/json",
                },
                body:JSON.stringify(this.getFormObject()),
            });
            this.displaySuccess();
        } catch(error){
            this.displayError(); //Mostrando erro de forma visual
            throw new Error(error); //Atirar erro no console
        }
        }

        init(){
            if(this.form) this.formButton.addEventListener("click", this.sendForm);
            return this;
        }

    }
    
    const formSubmit = new FormSubmit({
        form: "[data-form]",
        button: "[data-button]",
        success: "<h1 class='success'>Mensagem enviada!</h1>",
        error: "<h1 class='error'>Não foi possivel enviar sua mensagem</h1>",
    });

    formSubmit.init();

</script>

<template>
    <div id="form-container">
        <div id="texto">
            <Text/>
        </div>
        <form action="https://formsubmit.co/lian.mendes26@gmail.com" 
        method="POST" 
        data-form 
        id="formulario">
            <div class="inputs">
                <input name="nome" type="text" id="name" placeholder="Name" required>
                <input name="email" type="email" id="email" placeholder="Email address" required/>
                <input name="_subject" type="text" id="subject" placeholder="Subject" required/>
                <textarea name="message" id="message" placeholder="Message"></textarea>
                
            </div>
            <button type="submit">Send</button>
        </form>
    </div>
</template>

<style>
    #form-container{
        width: 450px;
        height: 600px;
        background-color: white;
        color: black;
        padding-top: 50px;
        border-radius: 10px;
        padding-left: 10px;       
    }
</style>