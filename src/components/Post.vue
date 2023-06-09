<template>
    <div class="px-4 max-w-[600px] mx-auto mt-10">
        <div class="flex items-center justify-between py-2">
            <div class="flex items-center">
                <router-link
                    :to="{
                        name: 'User',
                        params: {
                            id: post?.owner?._id || '',
                        },
                    }"
                    class="flex items-center"
                >
                    <img
                        class="rounded-full w-[38px] h-[38px]"
                        :src="post.owner.image"
                    />
                    <div class="ml-4 font-extrabold text-[15px]">
                        {{ post.owner.account }}
                    </div>
                </router-link>
                <div class="flex items-center text-[15px] text-gray-500">
                    <span class="-mt-5 ml-2 mr-[5px] text-[35px]">.</span>
                    <div>{{ getTimeGap(post.createdAt) }}</div>
                </div>
            </div>

            <DotsHorizontal
                v-if="user._id === post.owner._id"
                class="cursor-pointer"
                :size="27"
                @click="openPostOptionsOverlay"
            />
        </div>

        <div class="bg-black rounded-lg w-full min-h-[400px] flex items-center">
            <img
                class="w-full mx-auto"
                :src="post.image"
                @dblclick="likePost"
            />
        </div>

        <div class="flex justify-between">
            <div class="flex-1">
                <like-button :post="post" type="post"></like-button>
                <button class="-mt-[14px] ml-3" @click="openPostOverlay">
                    <CommentOutline class="pl-3 pt-[10px]" :size="30" />
                </button>
            </div>
            <save-button :post="post"></save-button>
        </div>

        <!-- <LikesSection :post="post" @like="updateLike($event)" /> -->

        <div class="py-1 font-extrabold text-black">
            {{ post.likes.length }} likes
        </div>
        <div class="mt-1">
            <span class="font-extrabold text-black">{{
                post.owner.account
            }}</span>
            {{ post.title }}
        </div>
        <div class="mt-1">
            <span>{{ post.description }}</span>
        </div>

        <button
            @click="openPostOverlay"
            class="py-1 font-extrabold text-gray-500"
        >
            View all {{ comments.length }} comments
        </button>

        <div class="mt-4 border-t border-gray-400"></div>
    </div>
</template>

<script>
import { computed, onMounted, ref, watchEffect } from 'vue';
import DotsHorizontal from 'vue-material-design-icons/DotsHorizontal.vue';
import CommentOutline from 'vue-material-design-icons/CommentOutline.vue';
import { useStore } from 'vuex';

import LikeButton from './LikeButton.vue';
import SaveButton from './SaveButton.vue';
import ShowPostOverlay from './ShowPostOverlay.vue';
import ShowPostOptionsOverlay from './ShowPostOptionsOverlay.vue';
import { getTimeGap } from '@/utils/time';
import commentService from '../services/commentService';
import postService from '../services/postService';

export default {
    name: 'Post',
    props: {
        post: {
            type: Object,
        },
    },
    components: {
        DotsHorizontal,
        CommentOutline,
        LikeButton,
        SaveButton,
        ShowPostOverlay,
        ShowPostOptionsOverlay,
    },
    setup(props) {
        const store = useStore();
        const comments = computed(() => {
            const c = store.state.post.posts.find(
                (item) => item._id === props.post._id
            );
            return c?.comments || [];
        });
        const isOpenPostOptionsOverlay = ref(false);
        const commentLength = computed(() => comments.value.length);

        watchEffect(() => comments);

        onMounted(async () => {
            let data = await commentService.getCommentByPostId(props.post?._id);

            store.dispatch('addCommentsToPost', {
                postId: props.post._id,
                comments: data,
            });
        });

        const user = computed(() => store.state.auth.user);

        const likePost = async () => {
            await postService.likePost(store, props.post);
        };

        const openPostOverlay = () => {
            store.dispatch('setCurrentPost', props.post);
            store.dispatch('setShowCurrentPostOverlay', true);
        };

        const openPostOptionsOverlay = () => {
            store.dispatch('setCurrentPost', props.post);
            store.dispatch('setShowCurrentPostOptionsOverlay', true);
        };

        return {
            user,
            getTimeGap,
            comments,
            commentLength,
            isOpenPostOptionsOverlay,
            openPostOverlay,
            openPostOptionsOverlay,
            likePost,
        };
    },
};
</script>
