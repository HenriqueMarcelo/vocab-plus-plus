<script setup>
import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'

import { ref } from 'vue'

const text = ref("");
const showWords = ref([]);

function convertWordList(wordList) {
  const newList = [];
  for (const word of wordList){
    if(word == '') {
      continue;
    }
    let chek = newList.find(c => c.word === word);
    if(!chek) {
      chek = {
        'word': word,
        'count': 1,
      };
      newList.push(chek);
    } else {
      chek.count ++;
    }
  }
  return newList;
}

function cleanString(dirtString) {
  return dirtString
    .replaceAll('.', ' ')
    .replaceAll('!', ' ')
    .replaceAll('?', ' ')
    .replaceAll(',', ' ')
    .replaceAll("'", ' ')
    .replaceAll('/n', ' ')
    .replaceAll('(', ' ')
    .replaceAll(')', ' ')
    .replaceAll('\n', ' ')
    ;
}

function save() {
  const lowString = text.value.toLowerCase()
  const cleanedString = cleanString(lowString);
  const wordArray = cleanedString.split(' ');
  const countedList = convertWordList(wordArray.sort());
  countedList.sort((x, y) => y.count - x.count);
  showWords.value = countedList;
}
</script>

<template>
  <section class="section">
    <header>
      <h1>
        Vocab++
      </h1>
    </header>
  
    <main>
      <textarea rows="30" v-model="text"></textarea>
    </main>
    
    <aside>
      <button @click="save"></button>

      <!-- <nav>
        <button></button>
        <button></button>
        <button></button>
      </nav> -->
      <ul>
        <li v-for="word in showWords">
          <label>
            <span>
              <input type="checkbox" name="" id=""> 
              {{word.word}}
            </span>
            <span class="count">
              {{word.count}}
            </span>
          </label>
        </li>
      </ul>
    </aside>
    <!-- <footer>
      Lorem ipsum dolor sit amet consectetur, adipisicing elit. Corrupti praesentium cumque excepturi nam laborum? 
      Unde earum consequatur temporibus? Commodi repudiandae exercitationem soluta similique voluptatum dolores sit, 
      deleniti iste rem odit modi quasi reprehenderit veniam corrupti repellat, neque vel, nobis qui perspiciatis 
      nesciunt illo culpa voluptates. Officia nulla, totam saepe sint esse laudantium deleniti debitis exercitationem.
    </footer> -->
  </section>

</template>

<style scoped lang="scss">
  h1 {
    font-size: 3rem;
    font-weight: bold;
  }
  section{
  max-width: 1280px;
  margin: 0 auto;
  padding: 2rem;

  font-weight: normal;
}

section{
  display: grid;
  grid-template-columns: 1fr 300px;
  grid-gap: 2rem;
}

button {
    border: .3rem solid var(--color-border);
    font-size: 2rem;
    height: 3rem;
    border-radius: 100px;
    cursor: pointer;
    background-color: var(--green);
    color: var(--white);
    width: 100%;
    margin-bottom: 2rem;

    &:hover { 
      filter: brightness(150%);
    }
  }

nav{
  display: flex;
  justify-content:space-between;
  margin-bottom: 2rem;
  
  button {
    border: .3rem solid var(--color-border);
    font-size: 2rem;
    height: 2.5rem;
    aspect-ratio: 2/1;
    border-radius: 100px;
    cursor: pointer;
    background-color: var(--yellow);
  
    &:first-child {
      background-color: var(--green);
    }
    &:last-child {
      background-color: var(--red);
    }
  }
}


ul {
  border: .3rem solid var(--color-border);
  border-radius: 1rem;
  background-color: white;
  list-style: none;
  padding: 0;
  padding: 2rem;
  height: calc(100% - 5.5rem);
  font-size: 1.3rem;
}

header {
  grid-column: 1 / 3;
}

footer {
  grid-column: 1 / 3;
}

textarea {
  border: .3rem solid var(--color-border);
  border-radius: 1rem;
  padding: 2rem;
  width: 100%;
  resize: none;
  font-size: 1.3rem;
}

label {
  display:flex;
  justify-content:space-between;
}

.count{
  background: var(--black);
  /* border: 3px solid var(--white); */
  color: var(--white);
  border-radius: 1rem;
  display: inline-block;
  padding: 0 0.5rem;
}
</style>
