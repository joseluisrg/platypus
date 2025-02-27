<template>
  <div class="menu">
    <section class="menu__mobile" tabindex="-1">
      <div class="menu__mobile-inner-container">
        <BasicLink
          class="
            menu__entry
            menu__home-link
          "
          kind="secondary"
          v-bind="homeLink"
        >
          <AppLogo
            class="menu__logo"
            :class="{ 'menu__logo_active': isActiveHome(homeLink) }"
          />
        </BasicLink>
        <label
          class="
            menu__hamburger-toggle
            menu__hamburger-toggle_menu-hidden
          "
          for="mobile-menu-toggle"
          data-test="mobile-menu-toggle"
        >
          <component :is="isMobileMenuVisible ? 'Close20' : 'Menu20'" />
        </label>
      </div>
      <input
        id="mobile-menu-toggle"
        v-model="isMobileMenuVisible"
        class="menu__mobile-menu-toggle"
        type="checkbox"
      >
      <MobileMenu
        class="menu__mobile-menu"
        :class="{ 'menu__mobile-menu_visible': isMobileMenuVisible }"
        :is-visible="isMobileMenuVisible"
        data-test="mobile-menu"
      />
    </section>
    <section class="menu__main-level">
      <nav class="menu__navigation-level">
        <AppLink
          class="
            menu__link
            menu__home-link
          "
          v-bind="homeLink"
          is-static="true"
        >
          <AppLogo
            class="menu__logo"
          />
        </AppLink>
        <template v-for="link in mainLevelLinks">
          <AppLink
            v-if="!link.sublinks"
            :key="link.url"
            class="menu__link"
            v-bind="link"
          >
            {{ $translate(link.label) }}
          </AppLink>
          <bx-dropdown
            v-else
            ref="communityDropdown"
            :key="link.url"
            v-model="selectedMenuItem"
            value="selectedMenuItem"
            class="menu__entry"
            :class="{ 'menu__entry_active': isCommunityActive() }"
            :trigger-content="selectedMenuItem"
            @bx-dropdown-selected="switchPanel($event)"
          >
            <bx-dropdown-item v-for="sublink in getSubLinks(link)" :key="`sublink:${sublink.url}`" :value="sublink.label">
              <AppLink
                class=" menu__entry menu__entry_second-level"
                :class="{ 'menu__entry_active': isActive(sublink) }"
                v-bind="appLinkFromNavLink(sublink)"
                kind="secondary"
                target="_self"
              >
                <p class="menu__entry-label">
                  {{ $translate(sublink.label) }}
                </p>
              </AppLink>
            </bx-dropdown-item>
          </bx-dropdown>
        </template>
        <bx-btn
          class="menu__account"
          kind="ghost"
          href="/account"
        >
          <User20 class="menu__account__icon" />
        </bx-btn>
      </nav>
    </section>
  </div>
</template>

<script lang="ts">
import { Options, mixins } from 'vue-class-component'
import Menu20 from '@carbon/icons-vue/lib/menu/20'
import Close20 from '@carbon/icons-vue/lib/close/20'
import User20 from '@carbon/icons-vue/lib/user/20'
import MenuMixin from '../../mixins/menu'
import AppLogo from '../common/AppLogo.vue'
import AppLink from '../common/AppLink.vue'
import BasicLink from '../common/BasicLink.vue'
import MobileMenu from './MobileMenu.vue'

// using Carbon web-component from https://github.com/carbon-design-system/carbon-web-components#basic-usage
import 'carbon-web-components/es/components/dropdown/dropdown.js'
import 'carbon-web-components/es/components/button/button.js'

@Options({
  components: {
    AppLink,
    AppLogo,
    MobileMenu,
    BasicLink,
    Menu20,
    Close20,
    User20
  }
})
export default class TheMenu extends mixins(MenuMixin) {
  isMobileMenuVisible: boolean = false
  selectedMenuItem: string = 'Community'

  created () {
    this.selectedMenuItem = this.$translate('Community')
  }

  switchPanel (event: any) {
    const selectionTitle = event.detail.item.value
    this.selectedMenuItem = selectionTitle
  }
}
</script>

<style lang="scss" scoped>
@import 'carbon-components/scss/globals/scss/typography';
@import '~/../scss/variables/mq.scss';
@import '~/../scss/mixins/mixins.scss';
@import '~/../scss/variables/colors.scss';

.menu {
  &__logo {
    height: 1.5rem;
    width: auto;
    color: $text-color-lighter;

    &_active {
      color: $text-active-color;
    }
  }

  &__link {
    @include type-style('body-long-02');
    display: inline-flex;
    flex-direction: column;
    justify-content: center;
    color: var(--link-color);
    text-decoration: none;
    margin-right: $spacing-09;

    &:hover {
      text-decoration: underline;
    }

    &:last-child {
      margin-right: 0;
    }

    &_active {
      color: $text-active-color;
    }
  }

  &__account {
    background-color: $button-background-color;
    &__icon {
      height: 2rem;
      width: 2rem;
      border: 2px solid $white-0;
      border-radius: 50%;
      fill: $white-0;
      box-sizing: border-box;
    }

    &::part(button) {
      background-color: $button-background-color;
      transition: background-color 70ms cubic-bezier(0, 0, 0.38, 0.9) 0s;
      padding-right: 15px;

      &:focus {
        border-color: $border-color-secondary;
        box-shadow: initial;
      }

      &:hover {
        background-color: $button-background-color-dark;
      }
    }
  }

  &__home-link {
    margin-left: 0;
    margin-right: auto;

    &:hover {
      text-decoration: none;
    }
  }

  &__hamburger-toggle {
    display: inline-flex;
    flex-direction: column;
    justify-content: center;
  }

  &__main-level {
    --link-color: #{$link-color-secondary};
  }

  &__mobile {
    position: relative;
    color: $text-active-color;

    @include mq($from: large) {
      display: none;
    }

    &:focus {
      outline: none;
    }
  }

  &__mobile-menu-toggle {
    // remove from page flow and hid
    visibility: hidden;
    position: absolute;
  }

  .menu__mobile-menu {
    position: fixed;
    top: 3.5rem; // taking into account the height of the top menu
    left: auto;
    bottom: 0;
    right: 0;
    z-index: 150;
    width: 12rem;
    box-shadow: 0 .5rem .5rem rgba(0,0,0,.25);
    visibility: hidden;
    pointer-events: none;

    &_visible {
      visibility: visible;
      pointer-events: all;
    }

    @include mq($until: medium) {
      width: 100%;
    }
  }

  &__mobile-inner-container,
  &__navigation-level {
    @include contained();
    padding-top: $spacing-05;
    padding-bottom: $spacing-05;
    display: flex;
    justify-content: flex-end;
    align-items: center;
  }

  &__navigation-level {
    padding-top: 0;
    padding-bottom: 0;
    padding-right: 0;
    @include mq($until: large) {
      display: none;
    }
  }

  &__logo {
    height: 1.5rem;
    width: auto;
    color: $text-color-lighter;

    &_active {
      color: $active-color;
    }
  }

  &__entry {
    @include type-style('body-long-02');
    flex: 0 0 auto;
    display: inline-flex;
    flex-direction: column;
    justify-content: center;
    color: var(--link-color);
    text-decoration: none;
    margin-right: $spacing-09;

    &:last-child {
      margin-right: 0;
    }

    // targeting specific links for consistent spacing
    &:nth-child(3) {
      margin-right: $spacing-07;
    }

    &:hover {
      color: var(--link-color);
      text-decoration: underline;
    }

    &:visited  {
      color: var(--link-color);
    }

    &_second-level {
      color: var(--link-color);
      @include type-style('body-long-02');
      display: block;
      margin-right: 0;
    }

    &-label {
      margin: 0;
      padding: .25rem;
    }

    // Like in TheTableOfContents component for combining modifiers.
    // TODO: make a function of this kind of constructions, something like
    // mod-combinator(&_active) {
    //  ...
    // }
    &_active#{&} {
      &_second-level,
      &_second-level:hover {
        color: $text-active-color;
      }
    }
  }

  &__home-link {
    margin-left: 0;
    margin-right: auto;

    &:hover {
      text-decoration: none;
    }
  }

  &__hamburger-toggle {
    display: inline-flex;
    flex-direction: column;
    justify-content: center;
  }
}

// Override component styling to match qiskit design
.menu {
  // select a child within the shadow
  bx-dropdown::part(trigger-button) {
    --cds-body-short-01-font-size: 1rem;
    background-color: $background-color-white;

    &:focus {
      outline: none;
    }
  }

  bx-dropdown[open]::part(trigger-button) {
    background-color: $background-color-lighter;
  }

  bx-dropdown::part(menu-body) {
    top: calc(100% + 10px);
    background-color: $background-color-lighter;
  }

  bx-dropdown.menu__entry {
    padding: .5rem 0;

    &[open] {
      background-color: $background-color-lighter;
      border-bottom: 1px solid $border-color;
      margin-bottom: -1px;
    }
  }

  bx-dropdown-item {
    display: flex;
    height: 100%;

    &:not(:last-child) .menu__entry-label {
      border-bottom: none;
      padding: 0;
    }
  }

  .bx--form-item {
    margin-right: $spacing-07;
  }

  .bx--dropdown {
    background: $background-color-white;
    height: initial;
    max-height: initial;
    border-bottom: 1px solid transparent;
  }

  .bx--dropdown--open {
    border-bottom: 1px solid $border-color;
  }

  .bx--dropdown--open,
  .bx--dropdown--open .bx--list-box__menu {
    background: $background-color-lighter;
  }

  .bx--list-box__menu {
    background-color: $background-color-white;

    &:focus {
      outline: none;
    }
  }

  // Dropdown button
  .bx--list-box__field {
    display: flex;
    height: calc(3.25rem);

    &:focus {
      outline: none;
    }

    &:hover .bx--list-box__label {
      text-decoration: underline;
    }
  }

  .bx--dropdown-item {
    position: relative;

    // using pseudo element to achieve partial underline
    &:first-child:after {
      content: '';
      border-bottom: 1px solid $border-color;
      position: absolute;
      width: 80%;
      left: 10%;
      bottom: 0;
    }

    &:hover {
      background-color: $background-color-light;
    }
  }

  .bx--list-box__label {
    @include type-style('body-long-02');
    color: var(--link-color);
  }

  .bx--list-box__menu {
    top: calc(3.25rem + 1px);
    box-shadow: initial;
  }

  .bx--list-box__menu-icon > svg {
    fill: var(--link-color);
  }
}
</style>
