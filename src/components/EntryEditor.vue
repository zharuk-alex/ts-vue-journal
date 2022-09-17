<script lang="ts" setup>
  import EmojiField from '@/components/EmojiField.vue';
  import ArrowCircleRight from '@/assets/icons/arrow-circle-right.svg';
  import type Emoji from '@/types/Emoji';
  import { ref, computed, onMounted, inject } from 'vue';
  import { userInjectionKey } from '@/injectionKeys';
  import type Entry from '@/types/Entry';

  // data
  const body = ref<string>('');
  const emoji = ref<Emoji | null>(null);
  const charCount = computed<number>(() => body.value.length);
  const maxChars = 280;
  const user = inject(userInjectionKey);

  // template refs
  const textarea = ref<HTMLTextAreaElement | null>(null);
  onMounted(() => textarea.value?.focus());

  // events
  const emit = defineEmits<{
    (e: 'create', entry: Entry): void;
  }>();

  // methods
  const handleSubmit = () => {
    emit('create', {
      body: body.value,
      emoji: emoji.value,
      createdAt: new Date(),
      userId: user?.id || 1,
      id: Math.random(),
    });

    body.value = '';
    emoji.value = null;
  };
  const handleTextInput = (e: Event) => {
    const textarea = e.target as HTMLTextAreaElement;
    if (textarea.value.length <= maxChars) {
      body.value = textarea.value;
    } else {
      body.value = textarea.value = textarea.value.substring(0, maxChars);
    }
  };
</script>
<template>
  <form class="entry-form" @submit.prevent="handleSubmit">
    <textarea
      ref="textarea"
      :value="body"
      @input="handleTextInput"
      :placeholder="`New Journal Entry for ${user?.username || 'anonymous'}`"
    ></textarea>
    <EmojiField v-model="emoji" />
    <div class="entry-form-footer">
      <span>{{ charCount }} / {{ maxChars }}</span>
      <button>Remember <ArrowCircleRight width="20" /></button>
    </div>
  </form>
</template>
