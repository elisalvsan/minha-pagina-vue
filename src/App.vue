<script setup>
import { ref, reactive, watch, computed } from 'vue'

let me = ref({})
let messages = reactive([])
let newMessage = ref(null)
let hasNewMessage = ref(false)
let actualMenu = ref(0)
let menu = [
    `Sobre Mim`,
    `Acadêmico`,
    `Profissional`,
    `Contatos`
]

let changeMenu = (menuIndex) => actualMenu.value = menuIndex
let sendMsg = () => {
    const date = new Date().toLocaleDateString("pt-BR")
    const text = newMessage.value
    messages.push({ date, text })
    newMessage.value = null
}

let closeAlert = () => hasNewMessage.value = false

const titlePage = computed(() => menu[actualMenu.value])

fetch('/aboutme.json')
    .then(response => response.json())
    .then(data => me.value = data)

watch(() => messages.length, (newMsg, OldMsg) => {
    if (newMsg > OldMsg) hasNewMessage.value = true
})
</script>

<template>
    <div class="layout">
        <header>
            <a v-for="(item, index) in menu" :key="'menuitem' + index" @click="changeMenu(index)">
                {{ item }}
            </a>

            <div class="alert" v-if="hasNewMessage">
                Nova mensagem no bate-papo.
                <a @click="closeAlert">X</a>
            </div>

        </header>
        <h2>{{ titlePage }}</h2>
        <main class="main">
            <section v-show="actualMenu === 0">
                <div class="wrapper">
                    <div>
                        <h3>{{ me.name }}</h3>
                        <br />
                        <p>{{ me.description }}</p>
                    </div>
                </div>
            </section>
            <section v-show="actualMenu === 1">
                <div v-for='(academic, index) in me.academic' :key="'academic' + index">
                    <div class="card">
                        <div class="card-body">
                            <h3 class="card-title">{{ academic.institution }}</h3>
                            <p class="card-text">{{ academic.course }}</p>
                            <p class="card-text">{{ academic.year }}</p>
                        </div>
                    </div>
                    <br />
                </div>
            </section>
            <section v-show="actualMenu === 2">
                <div v-for='(professional, index) in me.professional' :key="'professional' + index">
                    <div class="card">
                        <div class="card-body">
                            <h3 class="card-title">{{ professional.company }}</h3>
                            <p class="card-text">{{ professional.ocupation }}</p>
                            <p class="card-text">{{ professional.period }}</p>
                            <p class="card-text">{{ professional.jobs }}</p>
                        </div>
                    </div>
                    <br />
                </div>
            </section>
            <section v-show="actualMenu === 3">
                <div v-for="(contato, index) in me.contacts" :key="'contato' + index">
                    <div class="card">
                        <div class="card-body">
                            <h3 class="card-title">{{ contato.text }}</h3>
                            <p class="card-text">
                                <a target='_blank' v-if="contato.type === 'link'" v-bind:href="contato.contact">{{
                                    contato.contact }}</a>
                                <a target='_blank' v-else-if="contato.type === 'mail'"
                                    v-bind:href="`mailTo:${contato.contact}`">{{ contato.contact }}</a>
                                <span v-else>{{ contato.contact }}</span>
                            </p>
                        </div>
                    </div>
                    <br />
                </div>
            </section>
        </main>
        <footer>
            <h2 class="chat">Chat</h2>
            <span class="msg__chat" v-for="(msg, index) in messages">
                <p>{{ msg.text }}</p>
                <span>{{ msg.date }}</span>
            </span>
            <div class="form__chat">
                <textarea v-model="newMessage"></textarea>
                <button @click="sendMsg">Enviar</button>
            </div>
        </footer>
    </div>
</template>

<style scoped>
header {
    line-height: 1.5;
    display: flex;
    justify-content: center;
    font-size: 1.25rem;
    margin-bottom: 1.25rem;
    padding: 1rem;
    background-color: #f5f5f5;
    color: rgb(25, 195, 166);
}

@media (min-width: 1024px) {
    header {
        display: flex;
        place-items: center;
        padding-right: calc(var(--section-gap) / 2);
    }

    header a {
        margin: 0 1rem;
    }

    header .wrapper {
        display: flex;
        place-items: flex-start;
        flex-wrap: wrap;
    }

}

footer {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding-right: calc(var(--section-gap) / 2);
    padding-left: calc(var(--section-gap) / 2);
    text-align: center;
    width: 100%;
    height: 100%;
}

.chat {
    font-size: 20px;
    font-weight: 700;
    color: #3a4a5b;
    text-transform: uppercase;
    margin-bottom: 1.25rem;
    padding: 10px;
}

.msg__chat {
    font-size: 18px;
    color: #3a424b;
    margin-bottom: 1.25rem;

}

.form__chat {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 100%;
    margin: 0 auto;
    padding: 10px;

}

textarea {
    width: 60%;
    height: 100px;
    padding: 10px;
    font-size: 18px;
    border: 1px solid #ccc;
    border-radius: 5px;
    margin-bottom: 10px;
    resize: none;
    outline: none;
}
</style>
