+++
title = 'Sometimes we overcomplicate things'
description = 'How I overthink something and made the complex implementation and later realized that we can handle it in a better and simpler way'
date = 2024-06-01T17:21:27+05:45
draft = false
+++

How I overthink something and made the complex implementation and later realized that we can handle it in a better and simpler way. I was dealing with the form using Vue 3 and that form needs to be repeated once we click the add new button. First, I thought of creating a parent component called `Question` and inside that, it will consist of a `QuestionItem` component which we can use to loop for the repeater. Here I am using the concept of props, events, and watchers. Let's see how this will work.
```vue
// Question.vue
<script setup>
import { ref } from "vue";

const questions = ref([{ text: "", type: "" }]);

const addQuestion = () => {
  questions.value.push({ text: "", type: "" });
};

const updateQuestion = (index, updatedQuestion) => {
    questions.value[index] = updatedQuestion;
    console.log('questions', questions.value);
};

const handleSubmit = () => {
    // perform axios call with all the questions we have inside of questions.value
};
</script>

<template>
  <main>
    <QuestionItem
      v-for="(question, index) in questions"
      :modelValue="question"
      @update:modelValue="updateQuestion(index, $event)"
    />

    <button @click.prevent="addQuestion">Add Question</button>
    <button @click.prevent="handleSubmit">Submit</button>
  </main>
</template>

```

Here, I am creating a `questions` property to store all the questions. `addQuestion` will show a new form as a row, and `updateQuestion` will apply the changes that are made on the child component called `QuestionItem`. Here is the code inside the `QuestionItem` file.


```vue
// QuestionItem.vue
<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
    modelValue: Object,
});

const question = ref(props.modelValue.text || '');
const type = ref(props.modelValue.type || '');

const emit = defineEmits(['update:modelValue']);

watch(question, (newVal) => {
    emit('update:modelValue', { ...props.modelValue, text: newVal });
});

watch(type, (newVal) => {
    emit('update:modelValue', { ...props.modelValue, type: newVal });
});
</script>

<template>
    <h3>Question</h3>
    <input type="text" v-model="question" />
    <input type="text" v-model="type" />
</template>

```

Now, this was working as expected. We can add multiple questions with their types and once we click the submit button, we will have all the questions that are entered. But, wait! I was not happy with this implementation and later realized that we can make this very simple.

--- Simple & Better Approach

*Note: I was using the add question button and final submit button inside the parent component.*

Now, let's create different components. Let's say one is `Question` and another is `QuestionForm`. Let's see the code inside the `Question` component.

```vue
// Question.vue
<script setup>
import QuestionForm from "./QuestionForm.vue";
import { ref } from "vue";
</script>

<template>
  <main>
    <QuestionForm />

    // other stuff
  </main>
</template>

```

Here, we are just importing and using that `QuestionForm` component. Let's go inside the `QuestionForm` component to see the code.

```vue
// QuestionForm.vue
<script setup>
import { ref } from "vue";

const questions = ref([{ text: "", type: "" }]);

const addQuestion = () => {
  questions.value.push({ text: "", type: "" });
};

const handleSubmit = () => {
    // perform axios request with all the questions we have.
    console.log(questions.value);
};
</script>

<template>
  <div v-for="(question, index) in questions">
    <h3>Question</h3>
    <input type="text" v-model="question.text" />
    <input type="text" v-model="question.type" />
  </div>
  <button @click.prevent="addQuestion">Add Question</button>
  <button @click.prevent="handleSubmit">Submit</button>
</template>

```

Here, we are not introducing anything like watchers, props, and emitted events. We are just integrating that question repeater part inside the QuestionForm component itself. And, this is also working as expected, which is a much simpler and easier implementation than the previous one. By just rethinking, questioning our own implementation, and looking at things differently, we can come up with better solutions. I hope you enjoyed this article. Thank you for reading to the end :)
