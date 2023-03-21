<script setup>
import { ref } from "vue";

const text = ref("");
const showWords = ref([]);
const checkedWords = ref([]);

function getSavedWords() {
  let savedWords = window.localStorage.getItem("@vocab-plus-plus:words");
  if (!savedWords) {
    savedWords = [];
  } else {
    savedWords = JSON.parse(savedWords);
  }
  return savedWords;
}

function putCountInWordList(wordList) {
  const newList = [];
  for (const word of wordList) {
    if (word == "") {
      continue;
    }
    let chek = newList.find((c) => c.word === word);
    if (!chek) {
      chek = {
        word: word,
        count: 1,
      };
      newList.push(chek);
    } else {
      chek.count++;
    }
  }
  return newList;
}

function cleanString(dirtString) {
  return dirtString
    .replaceAll(".", " ")
    .replaceAll("!", " ")
    .replaceAll("?", " ")
    .replaceAll(",", " ")
    .replaceAll("'", " ")
    .replaceAll("/n", " ")
    .replaceAll("(", " ")
    .replaceAll(")", " ")
    .replaceAll("’", " ")
    .replaceAll("“", " ")
    .replaceAll("™", " ")
    .replaceAll("‘", " ")
    .replaceAll("”", " ")
    .replaceAll(":", " ")
    .replaceAll("®", " ")
    .replaceAll(";", " ")
    .replaceAll("/", " ")
    .replaceAll("|", " ")
    .replaceAll("©", " ")
    .replaceAll("—", " ")
    .replaceAll("1", " ")
    .replaceAll("2", " ")
    .replaceAll("3", " ")
    .replaceAll("4", " ")
    .replaceAll("5", " ")
    .replaceAll("6", " ")
    .replaceAll("7", " ")
    .replaceAll("8", " ")
    .replaceAll("9", " ")
    .replaceAll("0", " ")
    .replaceAll("\n", " ");
}

function updateNumberOfWordOnStorage(wordObject) {
  let savedWords = getSavedWords();
  let updatedWords = savedWords.map((savedWordObject) => {
    if (savedWordObject.word === wordObject.word) {
      savedWordObject.count += wordObject.count;
    }
    return savedWordObject;
  });

  window.localStorage.setItem(
    "@vocab-plus-plus:words",
    JSON.stringify(updatedWords)
  );
}

function updateNumberOfWordsOnStorage(words) {
  words.forEach((word) => {
    updateNumberOfWordOnStorage(word);
  });
}

function analize(userWhoTrigger = true) {
  const lowString = text.value.toLowerCase();
  const cleanedString = cleanString(lowString);
  const wordArray = cleanedString.split(" ");

  let savedWords = getSavedWords();
  const savedWordsString = savedWords.map((word) => word.word);

  const newWords = wordArray.filter((el) => !savedWordsString.includes(el));
  const countedList = putCountInWordList(newWords.sort());
  countedList.sort((x, y) => y.count - x.count);
  showWords.value = countedList;

  if (userWhoTrigger) {
    updateNumberOfWordsOnStorage(putCountInWordList(wordArray));
  }
}

function save() {
  let savedWords = getSavedWords();

  for (const word of checkedWords.value) {
    savedWords.push({
      word: word.word,
      count: word.count,
    });
  }

  window.localStorage.setItem(
    "@vocab-plus-plus:words",
    JSON.stringify(savedWords)
  );

  analize(false);
}

// function checkAll() {
//   const checkboxs = document.querySelectorAll("input[type=checkbox]");
//   checkboxs.forEach((checkbox) => {
//     setTimeout(() => {
//       checkbox.click();
//     }, 1);
//   });
// }
</script>

<template>
  <section class="section">
    <header>
      <h1>Vocab++</h1>
    </header>

    <main>
      <textarea rows="15" v-model="text"></textarea>
    </main>

    <aside>
      <button @click="analize">Analyze text</button>

      <ul>
        <li v-for="word in showWords" :key="word">
          <label>
            <span>
              <input
                type="checkbox"
                v-bind:value="{ word: word.word, count: word.count }"
                v-model="checkedWords"
              />
              {{ word.word }}
            </span>
            <span class="simpleCount"> [{{ word.count }}] </span>
          </label>
        </li>
      </ul>

      <!-- <button @click="checkAll">Check All</button> -->

      <button @click="save" :disabled="!checkedWords.length">
        Save as learned
      </button>
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
section {
  max-width: 1280px;
  margin: 0 auto;
  padding: 2rem;

  font-weight: normal;
}

section {
  display: grid;
  grid-template-columns: 1fr 300px;
  grid-template-rows: auto 1fr;
  grid-gap: 2rem;
  height: 100vh;

  @media (max-width: 768px) {
    grid-template-columns: 1fr 230px;
  }

  @media (max-width: 640px) {
    grid-template-columns: auto;
  }
}

button {
  border: 0.3rem solid var(--color-border);
  font-size: 1.5rem;
  padding: 0.5rem;
  border-radius: 100px;
  cursor: pointer;
  background-color: var(--green);
  color: var(--white);
  width: 100%;
  transition: 100ms;

  &.danger {
    background-color: var(--red);
  }

  &:not(:disabled):hover {
    filter: brightness(150%);
  }

  &:disabled {
    background: gray;
    cursor: not-allowed;
  }
}

aside {
  display: flex;
  flex-direction: column;
  gap: 2rem;
  height: calc(100vh - 10.8rem);
  min-height: 27.1rem;

  @media (max-width: 640px) {
    grid-column: 1 / 3;
    height: auto;
    min-height: auto;
  }
}

ul {
  border: 0.3rem solid var(--color-border);
  border-radius: 1rem;
  background-color: white;
  list-style: none;
  padding: 2rem 0;
  height: 100%;
  font-size: 1.3rem;
  overflow-y: auto;

  @media (max-width: 640px) {
    overflow-y: visible;
    height: auto;
  }
}

header {
  grid-column: 1 / 3;
}

footer {
  grid-column: 1 / 3;
}

main {
  @media (max-width: 640px) {
    grid-column: 1 / 3;
  }
}

textarea {
  border: 0.3rem solid var(--color-border);
  border-radius: 1rem;
  padding: 2rem;
  width: 100%;
  height: 100%;
  resize: none;
  font-size: 1.3rem;
}

li {
  label {
    display: flex;
    justify-content: space-between;
    padding: 0.2rem 2rem;
  }
  &:nth-child(odd) label {
    background: whitesmoke;
  }
}

// not good in the design
.count {
  background: var(--black);
  color: var(--white);
  border-radius: 100rem;
  display: flex;
  justify-content: center;
  align-items: center;
  aspect-ratio: 1;
  height: 2.3rem;
  font-family: monospace;
}

.simpleCount {
  font-family: monospace;
}
</style>
