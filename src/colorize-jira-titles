// ==UserScript==
// @name         Coloring jira tickets
// @namespace    https://github.com/D4ST1N/jira-tickets-coloring/
// @version      1.0.1
// @description  Coloring jira tickets
// @author       D4ST1N
// @license MIT
// @require      https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js
// @match        https://jira.betlab.com/*
// @grant        none
// ==/UserScript==

(function() {
    'use strict';

    Vue.component(
      'jiracolors',
      {
        template: `
          <div class="jc-root">
            <button class="jc-expand-button" type="button" @click="expandLegend">Show colors</button>
            <div v-show="legendShowed" class="js-legend">
              <div class="js-legend__overlay" @click.self="collapseLegend">
                <div class="js-legend__content">
                  <div v-for="item in statuses" :key="item.status" class="js-legend__item">
                    <span class="js-legend__item-status">{{ item.status }}</span>
                    <div class="js-legend__item-detail">
                      <span>Color: </span>
                      <input class="js-legend__item-color" type="color" :value="item.color" @change="changeColor(item, $event)">
                    </div>
                    <div class="js-legend__item-detail js-legend__item-detail--full">
                      <span>Opacity: {{ opacityInteger(item.opacity) }}%</span>
                      <input class="js-legend__item-opacity" type="range" min="0" max="100" :value="opacityInteger(item.opacity)" @change="changeOpacity(item, $event)" @input="changeOpacity(item, $event)">
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>`,
        data() {
          return {
            styles: document.createElement('style'),
            legendShowed: false,
            baseTemplate: `
              .ghx-issue [data-tooltip="Status: {status}" i]::before {
                background: {color};
                opacity: {opacity};
              }`,
            baseStyles: `
              .ghx-issue [data-tooltip~="Status:"]::before {
                content: '';
                position: absolute;
                width: 100%;
                height: 100%;
                opacity: {opacity};
                top: 0;
                left: 0;
                pointer-events: none;
              }
              .jc-root {
                position: fixed;
                bottom: 5px;
                left: 60px;
                z-index: 10;
              }
              .jc-expand-button {
                background: #455A64;
                color: #fff;
                padding: 6px 18px;
                font-size: 16px;
                border: 0;
                border-radius: 6px;
                box-shadow: 2px 2px 5px 0 #455A64;
                line-height: 1;
                cursor: pointer;
              }
              .js-legend {
                position: fixed;
                top: 0;
                left: 0;
                width: 100%;
                height: 100%;
              }
              .js-legend__overlay {
                display: flex;
                align-items: center;
                justify-content: center;
                width: 100%;
                height: 100%;
                background: rgba(69, 90, 100, .8);
              }
              .js-legend__content {
                background: #fff;
                padding: 20px 50px;
                display: flex;
                flex-wrap: wrap;
                max-width: 800px;
                justify-content: space-between;
              }
              .js-legend__item {
                font-size: 16px;
                width: 45%;
                margin-bottom: 20px;
                background: #E3F2FD;
                padding: 5px 15px;
                box-sizing: border-box;
                display: flex;
                justify-content: space-between;
                align-items: center;
                flex-wrap: wrap;
              }
              .js-legend__item-status {
                font-weight: bold;
              }
              .js-legend__item-color {
                margin-left: 10px;
              }
              .js-legend__item-opacity {
                width: 220px;
              }
              .js-legend__item-detail {
                display: flex;
                justify-content: space-between;
                align-items: center;
              }
              .js-legend__item-detail--full {
                width: 100%;
              }
              .ghx-issue .ghx-end, .ghx-issue.ghx-selected .ghx-end {
                box-shadow: none;
                background: transparent;
              }`,
            statuses:    [
              {
                status:  'Open',
                color:   '#607D8B',
                opacity: 0.25,
              },
              {
                status:  'Reopened',
                color:   '#607D8B',
                opacity: 0.25,
              },
              {
                status:  'Reopen',
                color:   '#607D8B',
                opacity: 0.25,
              },
              {
                status:  'System analysis',
                color:   '#9E9E9E',
                opacity: 0.25,
              },
              {
                status:  'Testing',
                color:   '#00BCD4',
                opacity: 0.25,
              },
              {
                status:  'In Progress',
                color:   '#8BC34A',
                opacity: 0.25,
              },
              {
                status:  'In development',
                color:   '#8BC34A',
                opacity: 0.25,
              },
              {
                status:  'Hold',
                color:   '#9E9E9E',
                opacity: 0.25,
              },
              {
                status:  'UAT',
                color:   '#9E9E9E',
                opacity: 0.25,
              },
              {
                status:  'Review',
                color:   '#009688',
                opacity: 0.25,
              },
              {
                status:  'Ready for Deploy',
                color:   '#2196F3',
                opacity: 0.25,
              },
              {
                status:  'Ready for Testing',
                color:   '#03A9F4',
                opacity: 0.25,
              },
              {
                status:  'Proposed to cancel',
                color:   '#FFC107',
                opacity: 0.25,
              },
              {
                status:  'Closed',
                color:   '#00C853',
                opacity: 0.25,
              },
              {
                status:  'Done',
                color:   '#00C853',
                opacity: 0.25,
              },
              {
                status:  'Canceled',
                color:   '#FF5722',
                opacity: 0.25,
              },
              {
                status:  'Rejected',
                color:   '#FF5722',
                opacity: 0.25,
              },
            ],
          };
        },
        mounted() {
          this.getStatuses();
          this.createStyles();
          this.updateStyles();
        },
        computed: {
          styleString() {
            return this.baseStyles.replace('{opacity}', this.baseOpacity);
          },
          baseOpacity() {
            return 0.25;
          },
        },
        methods:  {
          getStatuses() {
            try {
              const statuses = JSON.parse(localStorage.getItem('jc-statuses'));
              console.log(statuses);
              if (statuses) {
                this.statuses = statuses;
              }
            } catch (e) {}
          },
          setStatuses() {
            localStorage.setItem('jc-statuses', JSON.stringify(this.statuses));
          },
          expandLegend() {
            this.legendShowed = true;
          },
          collapseLegend() {
            this.legendShowed = false;
          },
          createStyles() {
            document.body.appendChild(this.styles);
          },
          buildStyles() {
            return this.statuses.map(item => this.baseTemplate
                                                 .replace('{status}', item.status)
                                                 .replace('{color}', item.color)
                                                 .replace('{opacity}', (item.opacity || this.baseOpacity))).join('');
          },
          updateStyles() {
            this.styles.innerHTML = `${this.styleString}${this.buildStyles()}`;
            this.setStatuses();
          },
          changeColor(item, event) {
            item.color = event.target.value;
            this.updateStyles();
          },
          changeOpacity(item, event) {
            item.opacity = Math.round(event.target.value) / 100;
            this.updateStyles();
          },
          opacityInteger(opacity) {
            return Math.round(opacity * 100);
          },
        },
      },
    );

    const appRoot = document.createElement('div');
    appRoot.id = 'app';
    appRoot.innerHTML = '<jiracolors></jiracolors>';
    document.body.appendChild(appRoot);
    new Vue(
      {
        el: '#app',
      },
    );
})();
