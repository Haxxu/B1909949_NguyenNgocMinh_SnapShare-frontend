<template>
    <div class="sidebar-container">
        <div class="brand">
            <router-link :to="{ name: 'Home' }">
                <h3>SnapShare</h3>
            </router-link>
        </div>
        <div class="actions">
            <div class="navigation">
                <router-link
                    :to="{ name: 'Home' }"
                    active-class="active"
                    class="link"
                >
                    <font-awesome-icon :icon="['fas', 'house']" />
                    <span>Home</span>
                </router-link>
                <router-link
                    :to="{ name: 'Search' }"
                    active-class="active"
                    class="link"
                >
                    <font-awesome-icon :icon="['fas', 'magnifying-glass']" />
                    <span>Search</span>
                </router-link>
                <router-link
                    :to="{ name: 'CreatePost' }"
                    active-class="active"
                    class="link"
                >
                    <font-awesome-icon :icon="['fas', 'square-plus']" />
                    <span>Create Post</span>
                </router-link>
                <router-link
                    :to="{
                        name: 'User',
                        params: {
                            id: user?._id || 'not-fun',
                            // id: userId || 'ksd',
                        },
                    }"
                    active-class="active"
                    class="link"
                >
                    <font-awesome-icon :icon="['fas', 'user']" />
                    <span>Profile</span>
                </router-link>
            </div>
            <div class="link" @click="logout">
                <font-awesome-icon
                    :icon="['fas', 'arrow-right-from-bracket']"
                />
                <span>Logout</span>
            </div>
        </div>
    </div>
</template>

<script>
import { useRouter } from 'vue-router';
import { computed, onBeforeMount, onMounted, watchEffect } from 'vue';
import { useStore } from 'vuex';

import authService from '@/services/authService.js';

export default {
    name: 'Sidebar',
    setup() {
        const router = useRouter();
        const store = useStore();

        onMounted(async () => {
            await authService.fetchUser(store);
        });

        const logout = async () => {
            await authService.logout(store);
            router.push({ name: 'Login' });
        };

        const user = computed(() => store.state.auth.user);

        const userId = computed(() =>
            store.state.auth.user?._id ? store.state.auth.user._id : ''
        );

        watchEffect(userId);

        return { logout, userId, user };
    },
};
</script>

<style lang="scss" scoped>
.sidebar-container {
    display: flex;
    flex-direction: column;
    width: 100%;
    height: 100%;
    padding: 10px 15px;

    .brand {
        height: 80px;
        width: 100%;
        display: flex;
        align-items: center;
        font-size: 30px;
        color: $text-primary;
        font-family: 'Belleza', 'Roboto', sans-serif;
        margin-bottom: 10px;

        a {
            text-decoration: none;
            color: inherit;
            display: block;
            padding: 25px 0 15px 10px;
        }
    }

    .actions {
        display: flex;
        flex: 1;
        flex-direction: column;
        justify-content: space-between;
    }

    .link {
        display: flex;
        align-items: center;
        font-size: 16px;
        padding: 8px 5px;
        color: $text-primary;
        text-decoration: none;
        cursor: pointer;
        transition: all 0.25s ease;

        // display: -webkit-box;
        // -webkit-box-orient: vertical;
        // -webkit-line-clamp: 1;

        svg,
        img {
            width: 25px;
            height: 25px;
            object-fit: contain;
        }

        span {
            margin-left: 10px;
            font-size: 16px;
            font-weight: 600;

            max-width: 250px;
            overflow: hidden;
            white-space: nowrap;
            text-overflow: ellipsis;
        }

        &:hover,
        &.active {
            color: $primary-color;
        }
    }

    .navigation {
        display: flex;
        flex-direction: column;

        .create-playlist-btn {
            margin-top: 20px;
        }

        .link {
            display: flex;
            justify-items: center;
            span {
                margin-left: 10px;
            }

            padding: 10px;
            margin: 10px 0;
            .icon {
                svg,
                img {
                    width: 17px;
                    height: 17px;
                    object-fit: contain;
                    color: $dark-green;
                }
            }
        }
    }
}
</style>
