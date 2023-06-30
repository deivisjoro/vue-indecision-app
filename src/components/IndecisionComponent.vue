<script setup>

    import { computed, reactive, ref, watch } from 'vue';

    const question = ref("");
    const answer   = reactive({
        answer: null,
        image: null
    })
    
    const isLoadedAnswer = ref(false);
    const isLoading = ref(false);

    const url = 'https://yesno.wtf/api';
    watch(question, async (newValue, oldValue)=>{
        isLoadedAnswer.value = false;
        if(!newValue.includes('?')) return; 

        isLoading.value = true;
        const data = await getAnswer();
        isLoading.value = false;
        isLoadedAnswer.value = true;
        answer.answer = data.answer;
        answer.image  = data.image;
        
    })

    const getAnswer = async () =>{
        const res = await fetch(url);
        return await res.json();
    }

    const traslateAnwer = computed(()=>{
        if(answer.answer==='maybe')
            return 'Tal vez!';
        else if(answer.answer==='yes')
            return 'Si!';
        else
            return 'No!'; 
    })
</script>

<template>
    <img v-if="answer.answer" :src="answer.image" alt="bg" />
    <div class="bg-dark"></div>
    <div class="indecision-container">
        <input type="text" placeholder="Hazme una pregunta" v-model="question">
        <p>Recuerda terminar con un signo de interrogacion (?)</p>

        <div v-if="isLoadedAnswer">
            <h2>{{ question }}</h2>
            <h3>{{ traslateAnwer }}</h3>
        </div>
        <div v-if="isLoading" class="loading">
            pensando...
        </div>
    </div>
</template>

<style scoped>
    img, .bg-dark {
        height: 100vh;
        left: 0px;
        max-height: 100%;
        max-width: 100%;
        position: fixed;
        top: 0px;
        width: 100vw;
    }

    .bg-dark {
        background-color: rgba(0, 0, 0, 0.4);
    }

    .indecision-container {
        position: relative;
        z-index: 99;
        text-align: center;
        margin-top: 50px;
    }
    
    input {
        width: 300px;
        font-size: 20px;
        padding: 10px 15px;
        border-radius: 5px;
        border: none;
    }
    input:focus {
        outline: none;
    }

    p {
        color: white;
        font-size: 20px;
        margin-top: 10px;
    }

    h2, h3, .loading {
        color: white;
    }
    
    h2, .loading {
        margin-top: 150px;
    }

    h3{
        margin-top: 20px;
        font-size: 2em;
    }

    .loading{
        font-size: 18px;
    }

</style>
