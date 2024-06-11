<script setup lang="ts">
import { computed, ref } from "vue";
import phonemesSrc from "./phonemes.json";

const text = ref("");

const phonemes = phonemesSrc.slice(0);
phonemes.sort((a, b) => b.grapheme.length - a.grapheme.length);

const syllables = computed(() => {
    const word = text.value;
    const letters = [];
    for (let i = 0; i < word.length; i++) {
        for (const p of phonemes) {
            const l = p.grapheme.length;
            if (word.substring(i, i + l) === p.grapheme) {
                letters.push(p);
                i += l - 1;
                break;
            }
        }
    }
    console.log(letters);
    return letters;
    // const syllables = [] as String[][];
    // return syllables;
});
</script>

<template>
    <input v-model="text">
    <div>
        <p>
            <span v-for="(s, i) in syllables" :key="i">
                {{ s.grapheme }}.
            </span>
        </p>
    </div>
</template>
