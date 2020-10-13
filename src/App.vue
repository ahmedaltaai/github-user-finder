<template>
  <div id="app">
    <header class="top">
      <nav>
        <a href="/">GitHub Search</a>
      </nav>
    </header>
    <main>
      <div class="searchbar__container">
        <div class="searchbar">
          <span>
            <input
              type="text"
              placeholder="Search..."
              id="placeholder"
              v-model="query"
              @focus="searchResultVisible = true"
              @keydown.esc="searchResultVisible = false"
              @input="softReset"
              @blur="searchResultVisible = false"
              @keyup.enter="getTheUser"
            />
            <label for="placeholder">
              <i class="fas fa-search"></i>
            </label>
            <i class="fas fa-times" v-if="query.length > 0" @click="reset"></i>
          </span>
        </div>

        <div
          v-if="query.length > 0 && searchResultVisible"
          class="search-result__container"
        >
          <div class="search-result">
            <div
              class="found pt-1"
              v-for="userFound in users"
              :key="userFound.id"
            >
              <a @click="showUserCard(userFound)">
                <img :src="userFound.avatar_url" alt="profile picture" />
                <span>{{ userFound.login }}</span>
              </a>
            </div>
            <div class="not-found pt-1">
              No results for '
              <strong>{{ query }}</strong
              >'
            </div>
          </div>
        </div>
      </div>
      <!-- ##### USER CARD ##### -->
      <UserCard v-if="cardVisibility">
        <template #header>
          <img :src="user.avatar_url" alt />
        </template>
        <template #body>
          <div class="content">
            <a :href="`https://github.com/${user.login}`">
              <h3>{{ user.name }}</h3>
            </a>
            <small>{{ user.login }}</small>
          </div>
          <div class="bio">
            <p>{{ user.bio }}</p>
          </div>
        </template>
        <template #footer>
          <a :href="`https://github.com/${user.login}?tab=followers`">
            <small>
              <i class="fas fa-user"></i>
              {{ user.followers }} Followers
            </small>
          </a>
          <small>
            <a :href="`https://github.com/${user.login}?tab=repositories`">
              <i class="fas fa-code-branch"></i>
              {{ user.public_repos }} Repos
            </a>
          </small>
        </template>
      </UserCard>
      <!-- ##### USER NOT FOUND CARD ##### -->
      <UserNotFound v-if="userNotFoundCard">
        <template #notFound>
          <h1>User Not Found</h1>
          <img src="./assets/not-found.jpg" alt="User Not Found 404" />
        </template>
      </UserNotFound>
    </main>
    <footer class="bottom">
      <a href="https://ahmedaltaai.com/">&copy; Ahmed Altaai</a>
    </footer>
  </div>
</template>

<script>
import axios from 'axios'
import UserCard from '@/components/UserCard'
import UserNotFound from '@/components/UserNotFound'

export default {
  name: 'App',
  components: { UserCard, UserNotFound },
  data() {
    return {
      query: '',
      searchResultVisible: false,
      noResultFor: false,
      cardVisibility: false,
      userNotFoundCard: false,
      user: [],
      users: []
    }
  },
  methods: {
    reset() {
      this.query = ''
    },
    softReset() {
      setTimeout(() => {
        axios
          .get(`https://api.github.com/search/users?q=${this.query}`)
          .then(res => {
            // if (res.data.total_count <= 30)
            this.users = res.data.items
          })
        this.searchResultVisible = true
      }, 1000)
    },
    showUserCard(userFound) {
      const loginName = userFound.login
      axios
        .get(`https://api.github.com/users/${loginName}`)
        .then(res => {
          this.user = res.data
          this.cardVisibility = true
          this.searchResultVisible = false
          this.query = ''
        })
        .catch(err => {
          if (err) {
            this.searchResultVisible = false
            this.noResultFor = true
          } else {
            this.searchResultVisible = true
            this.noResultFor = false
          }
        })
    },
    getTheUser() {
      axios
        .get(`https://api.github.com/users/${this.query}`)
        .then(res => {
          this.user = res.data
          this.cardVisibility = true
          this.userNotFoundCard = false
        })
        .catch(err => {
          if (err) {
            this.userNotFoundCard = true
            this.cardVisibility = false
          } else {
            this.cardVisibility = true
            this.userNotFoundCard = false
          }
        })
      this.query = ''
    }
  }
}
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  @import url('https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,100;0,300;0,400;0,500;0,700;0,900;1,100;1,300;1,400;1,500;1,700;1,900&display=swap');
  font-family: 'Roboto', sans-serif;
  text-decoration: none;
}

#app {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
}

main {
  margin: 2em auto;
}

header,
footer {
  z-index: 2;
  height: 3em;
  background-color: #333;
  color: #eee;
  width: 100%;
  display: flex;
  align-items: center;

  nav {
    padding: 0 1em;
    a {
      color: #eee;
    }
  }
}

.top {
  position: fixed;
  left: 0;
  top: 0;
}

.bottom {
  position: fixed;
  left: 0;
  bottom: 0;
}

footer {
  height: 3em;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 3em;
  background-color: #333;
  color: #eee;
  width: 100%;
  display: flex;
  align-items: center;

  color: #eee;
  a {
    color: #eee;
  }
}

.searchbar__container {
  position: relative;
  margin-top: 3em;

  .searchbar {
    display: flex;
    justify-content: center;
  }

  .searchbar span {
    position: relative;
    input {
      position: relative;
      border: 1px solid #eee;
      border-radius: 20px;
      padding: 0.7em 3em;
      width: 20em;

      &[placeholder] {
        color: #777;
      }

      &:focus {
        border: 1px solid #007bff;
      }
    }

    i {
      cursor: pointer;
    }

    .fa-search {
      position: absolute;
      z-index: 1;
      top: 50%;
      left: 5%;
      transform: translateY(-50%);
      color: #aaa;
    }

    .fa-times {
      position: absolute;
      z-index: 1;
      top: 50%;
      right: 6%;
      transform: translateY(-50%);
      color: #aaa;
    }
  }

  .search-result__container {
    position: absolute;
    z-index: 1;
    left: 50%;
    transform: translateX(-50%);
    width: 100%;
    border: 1px solid #ddd;
    border-radius: 10px;
    text-align: left;
    margin-top: 0.5em;
    background-color: #fff;

    .found {
      border-bottom: 1px solid #eee;

      a {
        display: flex;
        justify-content: space-between;
        padding: 0.5em 0;

        img {
          border-radius: 50%;
          width: 64px;
        }
        span {
          display: flex;
          flex-direction: column;
          color: red;
        }
      }

      &:hover {
        background-color: #eee;
        border-radius: 10px 10px 0 0;
        cursor: pointer;
      }
    }

    .pt-1 {
      padding: 1em;
    }
  }
}

.card__container {
  width: 20em;

  img {
    width: 100%;
    border-radius: 5px 5px 0 0;
  }

  .body {
    padding: 1em;
    a {
      color: #111;
    }
    .content {
      margin-bottom: 1em;
    }
  }

  .footer {
    padding: 0.75em 1em;
    border-top: 1px solid #eee;
    display: flex;
    justify-content: space-between;
    i {
      margin-right: 0.5em;
    }
    a {
      color: #999;
    }
  }

  small {
    color: #999;
  }
}

.notFoundCard__container {
  display: flex;
  flex-direction: column;
  align-items: center;
  box-shadow: 0 0 5px 5px #eee;
  padding: 1em;
  img {
    width: 256px;
  }
}
</style>
