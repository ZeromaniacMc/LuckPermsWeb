<template>
  <main class="download">
    <section class="hero">
      <div class="container">
        <h1>{{ $t('download.title') }}</h1>
        <div class="version">
          <p><span>v{{ version }}</span></p>
          <p>{{ $t('download.build', { time: relativeTimestamp }) }}</p>
          <font-awesome icon="asterisk" :spin="true" v-if="!version" />
        </div>
      </div>
    </section>

    <div class="container">
      <section class="resources">
        <div>
          <h2>{{ $t('download.typeChoose') }}</h2>
          <a :href="downloads.bukkit" v-on:click="logDownload('bukkit')" class="resource">
            <span>
              <img src="@/assets/logos/bukkit.png" alt="Bukkit">
              Bukkit
            </span>
            <small>{{ $t('download.bukkit') }}</small>
          </a>

          <div class="resource" v-for="(item, index) in accordion1Items" :key="index" @click="toggleAccordion(1, index); rotateIcon(1, index)">
            <span>
              <img src="@/assets/logos/sponge.png" alt="Sponge">
                {{ item.title }}
              <small>Click to view available versions</small>
              <font-awesome icon="caret-right" fixed-width :class="{ 'rotate-icon': item.rotated }" style="margin-left: auto; font-size: 28px;"/>
            </span>
            <div v-show="item.open">
              <ul class="versionlist">
                <li v-for="(link, linkIndex) in item.links" :key="linkIndex">
                  <a :href="link.url">{{ link.text }}</a>
                </li>
              </ul>
              <small>Hint: The last version to support API 7 is minecraft 1.12.2</small>
            </div>
          </div>

          <div class="resource" v-for="(item, index) in accordion2Items" :key="index" @click="toggleAccordion(2, index); rotateIcon(2, index)">
            <span>
              <img src="@/assets/logos/fabric.png" alt="Fabric">
                {{ item.title }}
              <small>Click to view available versions</small>
              <font-awesome icon="caret-right" fixed-width :class="{ 'rotate-icon': item.rotated }" style="margin-left: auto; font-size: 28px;"/>
            </span>
            <div v-show="item.open">
              <ul class="versionlist">
                <li v-for="(link, linkIndex) in item.links" :key="linkIndex">
                  <a :href="link.url">{{ link.text }}</a>
                </li>
              </ul>
            </div>
          </div>

          <div class="resource" v-for="(item, index) in accordion3Items" :key="index" @click="toggleAccordion(3, index); rotateIcon(3, index)">
            <span>
              <img src="@/assets/logos/forge.png" alt="Forge">
                {{ item.title }}
              <small>Click to view available versions</small>
              <font-awesome icon="caret-right" fixed-width :class="{ 'rotate-icon': item.rotated }" style="margin-left: auto; font-size: 28px;"/>
            </span>
            <div v-show="item.open">
              <ul class="versionlist">
                <li v-for="(link, linkIndex) in item.links" :key="linkIndex">
                  <a :href="link.url">{{ link.text }}</a>
                </li>
              </ul>
            </div>
          </div>

          <!-- things i broke:
            The translation keys can no longer be used 

              <small>{{ $t('download.sponge') }}</small>
              <small>{{ $t('download.fabric') }}</small>
              <small>{{ $t('download.forge') }}</small>

            Due to the change from <a> to <div> as main container, the border highlighting on click broke
            
              <a :href="downloads.sponge" v-on:click="logDownload('sponge')" class="resource">
              <a :href="downloads.fabric" v-on:click="logDownload('fabric')" class="resource">
              <a :href="downloads.forge" v-on:click="logDownload('forge')" class="resource">

            TODO: 
              
              - check whether all needed versions are listed and linked properly
              - for longer lists (probably fabric) consider splitting it in half (2 containers)
              - get feedback about style and functionality
              - check if there's anything that can be optimized
              - fix spacing and beautify
              - annoy turbo about all of the above
              - open pr
              - jump off a bridge

          -->

          <a :href="downloads.nukkit" v-on:click="logDownload('nukkit')" class="resource">
            <span>
              <img src="@/assets/logos/nukkit.png" alt="Nukkit">
              Nukkit
            </span>
            <small>{{ $t('download.nukkit') }}</small>
          </a>
          <a :href="downloads.velocity" v-on:click="logDownload('velocity')" class="resource">
            <span>
              <img src="@/assets/logos/velocity.png" alt="Velocity">
              Velocity
            </span>
            <small>{{ $t('download.velocity') }}</small>
          </a>
          <a :href="downloads.bungee" v-on:click="logDownload('bungee')" class="resource">
            <span>
              <img src="@/assets/logos/bungeecord.png" alt="BungeeCord">
              BungeeCord
            </span>
            <small>{{ $t('download.bungee') }}</small>
          </a>
          <a :href="downloads['bukkit-legacy']" v-on:click="logDownload('bukkit-legacy')" class="resource">
            <span>
              <img src="@/assets/logos/bukkit.png" alt="Bukkit">
              Bukkit Legacy
            </span>
            <small>{{ $t('download.bukkitLegacy') }}</small>
          </a>
          <button class="button" @click="openQuiz">
            <font-awesome icon="question-circle" />
            {{ $t('download.typeHelp') }}
          </button>
        </div>

        <div>
          <h2>{{ $t('download.changelog') }}</h2>
          <ul class="changelog">
            <li v-for="entry in changeLog" :key="entry.version">
              <span>
                <a :href="`https://github.com/LuckPerms/LuckPerms/commit/${entry.commit}`" target="_blank">
                  <code>v{{ entry.version }}</code>
                </a>
                <span class="title">{{ entry.title }}</span>
              </span>
              <span class="time lighter">{{ relativeDate(entry.timestamp) }}</span>
            </li>
          </ul>
          <h2>{{ $t('download.install.title') }}</h2>
          <ol>
            <li v-html="$t('download.install.add')" />
            <li v-html="$t('download.install.restart')" />
            <li v-html="$t('download.install.config')" />
            <i18n path="download.install.setup" tag="li">
              <template #wiki>
                <router-link to="wiki/Usage">
                  {{ $t('download.install.wiki') }}
                </router-link>
              </template>
            </i18n>
          </ol>
          <h2>{{ $t('download.trouble.title') }}</h2>
          <ul>
            <li v-html="$t('download.trouble.console')" />
            <i18n path="download.trouble.read" tag="li">
              <template #wiki>
                <router-link to="wiki/Installation">
                  {{ $t('download.trouble.wiki') }}
                </router-link>
              </template>
            </i18n>
            <i18n path="download.trouble.support" tag="li">
              <template #discord>
                <a href="https://discord.gg/luckperms" target="_blank">Discord</a>
              </template>
            </i18n>
          </ul>
        </div>
      </section>
    </div>
    <section class="hero extensions">
      <div class="container">
        <div>
          <h1>{{ $t('download.extensions.title') }}</h1>
          <i18n path="download.extensions.description" tag="p">
            <template #wiki>
              <router-link to="/wiki/Extensions">
                {{ $t('download.extensions.descriptionWiki') }}
              </router-link>
            </template>
          </i18n>
        </div>
      </div>
    </section>
    <div class="container extensions">
      <section class="resources">
        <div>
          <a :href="extensions['extension-legacy-api']" v-on:click="logDownload('extension-legacy-api')" class="resource">
            <span>
              <font-awesome icon="arrow-alt-circle-down" />
              {{ $t('download.extensions.legacy') }}
            </span>
            <small>{{ $t('download.extensions.version') }}</small>
          </a>
          <div>
            <p>{{ $t('download.extensions.legacyInfo') }}</p>
            <i18n path="download.extensions.more" tag="p">
              <template #wiki>
                <router-link to="/wiki/Extensions#extension-legacy-api">
                  {{ $t('download.extensions.wiki') }}
                </router-link>
              </template>
            </i18n>
          </div>
        </div>
        <div>
          <a :href="extensions['extension-default-assignments']" v-on:click="logDownload('extension-default-assignments')"
            class="resource">
            <span>
              <font-awesome icon="arrow-alt-circle-down" />
              {{ $t('download.extensions.defaultAssignments') }}
            </span>
            <small>{{ $t('download.extensions.version') }}</small>
          </a>
          <div>
            <i18n path="download.extensions.defaultAssignmentsInfo" tag="p">
              <template #wiki>
                <router-link to="/wiki/Default-Groups">
                  {{ $t('download.extensions.groups') }}
                </router-link>
              </template>
            </i18n>
            <p>Check out the <router-link to="/wiki/Extensions#extension-default-assignments">wiki
                section</router-link> for more information. See also
              <a href="/wiki/Default-Groups#configure-default-assignments">this section</a> about
              configuring default assignments.
            </p>
          </div>
        </div>
      </section>
    </div>
    <section class="hero additional-plugins">
      <div class="container">
        <div>
          <h1>Additional Plugins</h1>
          <p>
            Additional plugins can provide more complex features,
            but may not be available on all platforms
            .
          </p>
        </div>
      </div>
    </section>
    <div class="container additional-plugins">
      <section class="resources">
        <div>
          <a :href="additionalPlugins['extracontexts']" class="resource">
            <span>
              <font-awesome icon="arrow-alt-circle-down" />
              ExtraContexts Plugin
            </span>
            <small>LuckPerms 5.0 and above, Bukkit only</small>
          </a>
          <div>
            <p>Add more contexts, including some for other plugins</p>
          </div>
        </div>
      </section>
    </div>
    <section class="hero placeholder-expansions">
      <div class="container">
        <div>
          <h1>Placeholder Expansions</h1>
          <p>
            LuckPerms adds
            <router-link to="/wiki/Placeholders#placeholders">placeholders</router-link>
            to PlaceholderAPI and MVdWPlaceholderAPI
            .
          </p>
        </div>
      </div>
    </section>
    <div class="container placeholder-expansions">
      <section class="resources">
        <div>
          <a :href="placeholderExpansions['bukkit-placeholderapi']" class="resource">
            <span>
              <font-awesome icon="arrow-alt-circle-down" />
              PlaceholderAPI
            </span>
            <small>LuckPerms 5.0 and above, Bukkit only</small>
          </a>
          <div>
            <p>
              Install using either
              <code>/papi ecloud download LuckPerms</code>
              or by
              <router-link to="/wiki/Placeholders#manual-install">installing manually</router-link>
              .
            </p>
          </div>
        </div>
        <div>
          <a :href="placeholderExpansions['bukkit-mvdw']" class="resource">
            <span>
              <font-awesome icon="arrow-alt-circle-down" />
              MVdWPlaceholderAPI
            </span>
            <small>LuckPerms 5.0 and above, Bukkit only</small>
          </a>
          <div>
            <p>Place the JAR file in your <code>/plugins/</code> folder.</p>
          </div>
        </div>
        <div>
          <a :href="placeholderExpansions['fabric-placeholderapi']" class="resource">
            <span>
              <font-awesome icon="arrow-alt-circle-down" />
              Fabric PlaceholderAPI
            </span>
            <small>LuckPerms 5.0 and above, Fabric only</small>
          </a>
          <div>
            <p>Place the JAR file in your <code>/mods/</code> folder.</p>
          </div>
        </div>
      </section>
    </div>

    <transition name="fade">
      <Quiz v-if="quiz.open" :downloads="downloads" @close="quiz.open = false" />
    </transition>
  </main>
</template>

<script>
import { relativeDate } from '@/util/date';

export default {
  name: 'Download',
  metaInfo: {
    title: 'Download',
  },
  components: {
    Quiz: () => import('../components/Download/Quiz'),
  },
  data() {
    return {
      quiz: {
        open: false,
        rotated: false,
      },
      accordion1Items: [
        {
          title: "Sponge",
          open: false,
          rotated: false,
          links: [
            {
              text: "API 8",
              url: "downloads.sponge"
            },
            {
              text: "API 7",
              url: "https://example.com/link1-2"
            }
          ]
        },
        // Add more items for the sponge accordion if needed here
      ],
      accordion2Items: [
        {
          title: "Fabric",
          open: false,
          rotated: false,
          links: [
            {
              text: "1.20.1 +",
              url: "https://www.curseforge.com/minecraft/mc-mods/luckperms/files/4587307"
            },
            {
              text: "1.19.4",
              url: "https://www.curseforge.com/minecraft/mc-mods/luckperms/files/4443552"
            },
            {
              text: "1.19.3",
              url: "https://www.curseforge.com/minecraft/mc-mods/luckperms/files/4370529"
            },
            {
              text: "1.19.2",
              url: "https://www.curseforge.com/minecraft/mc-mods/luckperms/files/3995680"
            },
            {
              text: "1.19",
              url: "https://www.curseforge.com/minecraft/mc-mods/luckperms/files/3896471"
            },
            {
              text: "1.18.2",
              url: "https://www.curseforge.com/minecraft/mc-mods/luckperms/files/3807225"
            }
          ]
        },
        // Add more items for the fabric accordion if needed here
      ],
      accordion3Items: [
        {
          title: "Forge",
          open: false,
          rotated: false,
          links: [
            {
              text: "1.19",
              url: "https://www.curseforge.com/minecraft/mc-mods/luckperms/files/3896470"
            },
            {
              text: "1.18.2",
              url: "https://www.curseforge.com/minecraft/mc-mods/luckperms/files/3828099"
            }
          ]
        },
        // Add more items for the forge accordion if needed here
      ]
    };
  },
  computed: {
    extensions() { return this.$store.getters.extensions; },
    additionalPlugins() { return this.$store.getters.additionalPlugins; },
    placeholderExpansions() { return this.$store.getters.placeholderExpansions; },
    downloads() { return this.$store.getters.downloads; },
    version() { return this.$store.getters.version; },
    versionTimestamp() { return this.$store.getters.versionTimestamp; },
    relativeTimestamp() {
      if (this.versionTimestamp) {
        return relativeDate(this.versionTimestamp, this.$i18n.locale, new Date().getTime(), true);
      }
      return null;
    },
    changeLog() { return this.$store.getters.changeLog; },
  },
  methods: {
    logDownload(platform) {
      // eslint-disable-next-line no-undef
      plausible('Download', { props: { type: platform } });
    },
    openQuiz() {
      this.quiz.open = true;
    },
    closeQuiz() {
      this.quiz.open = false;
    },
    relativeDate(value) {
      return relativeDate(value, this.$i18n.locale);
    },
    toggleAccordion(accordionIndex, itemIndex) {
      switch (accordionIndex) {
        case 1:
          this.accordion1Items[itemIndex].open = !this.accordion1Items[itemIndex].open;
          break;
        case 2:
          this.accordion2Items[itemIndex].open = !this.accordion2Items[itemIndex].open;
          break;
        case 3:
          this.accordion3Items[itemIndex].open = !this.accordion3Items[itemIndex].open;
          break;
        default:
          break;
      }
    },
    rotateIcon(accordionIndex, itemIndex) {
      if (accordionIndex === 1) {
        this.accordion1Items[itemIndex].rotated = !this.accordion1Items[itemIndex].rotated;
      } if (accordionIndex === 2) {
        this.accordion2Items[itemIndex].rotated = !this.accordion2Items[itemIndex].rotated;
      } else if (accordionIndex === 3) {
        this.accordion3Items[itemIndex].rotated = !this.accordion3Items[itemIndex].rotated;
      }
    }
  },
};
</script>

<style lang="scss">
main.download {
  overflow-y: auto;

  .hero {
    .container {
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 4rem;

      @include breakpoint($md) {
        flex-direction: row;
        align-items: center;
        justify-content: space-between;
      }
    }

    .version {
      line-height: 1.2;
    }

    h1 {
      text-align: center;

      @include breakpoint($md) {
        text-align: left;
      }
    }

    p {
      text-align: center;
      font-size: 1.5rem;
      opacity: 1;
      color: rgba(225, 255, 255, .5);

      @include breakpoint($md) {
        text-align: right;
      }
    }

    span {
      color: $brand_color;
      font-weight: bold;
      font-size: 2.2em;
    }
  }

  .resource {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    padding: 1.25rem 1.5rem;

    @include breakpoint($md) {
      flex-direction: row;
      align-items: center;
    }

    span {
      margin: 0 1rem 0 0;
      white-space: nowrap;
      display: flex;
      flex-direction: row;
      align-items: center;
    }

    small {
      margin-top: 1rem;
      font-weight: normal;

      @include breakpoint($md) {
        margin: 0;
      }
    }

    img {
      margin-right: .75rem;
      width: 1.5em;
      filter: saturate(20%);
    }

    &:hover img {
      filter: none;
    }
  }

  .button {
    color: $brand-color;
    background-color: $grey;

    &:hover {
      background: lighten($grey, 10%);
    }

    svg {
      opacity: .5;
      margin-right: 1rem;
    }
  }

  .changelog {
    list-style: none;
    padding: 0;

    li {
      padding-bottom: .25rem;
      margin-bottom: .25rem;
      display: flex;
      justify-content: space-between;

      &:not(:last-child) {
        border-bottom: 1px solid rgba(255, 255, 255, .1);
      }

      >span {
        display: flex;
      }

      .title {
        margin: 0 1rem;
      }

      .time {
        flex-shrink: 0;
      }
    }
  }

  .extensions,
  .additional-plugins,
  .placeholder-expansions {
    &.hero {
      .container {
        justify-content: center;
      }

      h1,
      p {
        text-align: center;
      }
    }

    .resources {
      >div {
        +div {
          padding-top: 0;

          @include breakpoint($md) {
            padding-top: 4rem;
          }
        }
      }
    }
  }

  .additional-plugins {
    section {
      justify-content: center;
    }
  }

  .rotate-icon {
    transform: rotate(90deg);
  }

  .versionlist {
    list-style: none;
    margin: 0 0 0 0;
    padding-left: 3rem;

    > li {
      padding-bottom: .25rem;
      text-decoration: none;
      line-height: 1;

      &:first-child {
        padding-top: .25rem;
      }

      a {
        text-decoration: none;
        font-size: 18px;
        color: white;
      }
    }
  }

/* consider moving this to scss->common */
  div.resource {
    display: block;
    background: $grey;
    color: $brand-color;
    padding: 1.5rem;
    line-height: 1;
    text-decoration: none;
    border-radius: 2px;
    font-size: 1.5em;
    transition: background .2s;
    box-shadow: 0 0 1rem rgba(0,0,0,.1);

    &:not(:last-child) {
      margin-bottom: 2rem;
    }

    &:hover {
      background: lighten($grey, 10%);
    }

    span {
      font-weight: bold;
      margin: 0 0 0 0;
    }

    svg {
      margin-right: .5rem;
      opacity: .5;
    }

    small {
      color: white;
      opacity: .4;
      font-size: 1rem;
      margin-left: 1rem;
    }
  }
}
</style>
