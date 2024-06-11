<script setup lang="ts">
import { computed, ref } from "vue";

import type { Phoneme } from "./phonemes";
import phonemesSrcDb from "./phonemes.json";
import wordsDb from "./words.json";

const text = ref("");

const phonemesDb = phonemesSrcDb.slice(0) as Phoneme[];
phonemesDb.sort((a, b) => b.grapheme.length - a.grapheme.length);

const phonemes = computed(() => {
    const word = text.value;
    const phonemes = [] as Phoneme[];
    for (let i = 0; i < word.length; i++) {
        for (const p of phonemesDb) {
            const l = p.grapheme.length;
            if (word.substring(i, i + l) === p.grapheme) {
                phonemes.push(p);
                i += l - 1;
                break;
            }
        }
    }
    return phonemes;
});


const syllables = computed(() => {
    const syllables = phonemes.value.slice(0) as (Phoneme | Phoneme[])[];
    for (let i = 0; i < syllables.length; i++) {
        const v = syllables[i] as Phoneme;
        console.log(v);
        if (v.tags.includes('vowel')) {
            let s = [v];
            const c = syllables[i - 1];
            if (!Array.isArray(c) && c?.tags.includes('consonant')) {
                s = [c, ...s];
                syllables.splice(i - 1, 1)
            }
            i -= s.length - 1;
            syllables[i] = s;
        }
    }

    for (let i = 0; i < syllables.length; i++) {
        const c = syllables[i];
        if (!Array.isArray(c)) {
            const s = syllables[i - 1] as Phoneme[];
            if (Array.isArray(s)) {
                s.push(c);
                syllables.splice(i, 1)
            }
            else {
                syllables[i] = [c];
            }
            i--;
        }
    }
    return syllables as Phoneme[][];
});
</script>

<template>
    <div class="root">
        <div class="buttons">
            <div v-for="(w, i) in wordsDb" :key="i" @click="text = w">
                {{ w }}
            </div>
        </div>
        <input v-model="text">
        <div class="word">
            <div v-for="(s, i) in syllables" :key="i" class="syllable">
                <span v-for="(l, j) in s" :key="j" :title="l.ipa" class="phoneme">
                    {{ l.grapheme }}
                </span>
            </div>
        </div>
        <div class="word">
            <div v-for="(s, i) in syllables" :key="i" class="syllable">
                <span v-for="(l, j) in s" :key="j" :title="l.ipa" class="phoneme">
                    {{ l.ipa }}
                </span>
            </div>
        </div>
    </div>
</template>

<style>
.buttons {
    display: flex;
    gap: 4px;
    font-size: small;
    margin: 4px;

    div {
        font-weight: 500;
        padding: 2px;
        background-color: #f0f9ff;
        border-radius: 4px;
        cursor: pointer;
    }

    div:hover {
        background-color: #e0f2fe;

    }
}

.root {
    display: flex;
    flex-direction: column;
}

.word {
    display: flex;
    flex-wrap: nowrap;
    padding: 4px;
}

.syllable {
    border-radius: 2px;
    display: flex;
    gap: 2px;
    padding: 2px 4px;
}

.syllable:hover {
    background-color: #e0f2fe;
}

.phoneme {
    background-color: #bae6fd;
    border-radius: 2px;
    padding: 0 2px;
    font-weight: 500;
}
</style>
