<i18n>
  {
    "zh": {
      "opensAt": "开放时间：{0} ~ {1}",
      "zone": {
        "name": "章节",
        "types": {
          "MAINLINE": "主线",
          "WEEKLY": "物资筹备",
          "ACTIVITY_OPEN": "限时活动（开放中）",
          "ACTIVITY_CLOSED": "限时活动（已结束）"
        },
        "status": {
          "closed": "已结束",
          "open": "开放中"
        }
      },
      "stage": {
        "name": "关卡",
        "apCost": "{apCost} 点理智",
        "loots": {
          "normal": "常规掉落",
          "extra": "额外物资",
          "special": "特殊掉落"
        }
      },
      "stats": {
        "name": "统计结果",
        "title": "{stage} 统计结果"
      }
    },
    "en": {
      "opensAt": "Opens At: {0} ~ {1}",
      "zone": {
        "name": "Zone",
        "types": {
          "MAINLINE": "Mainline",
          "WEEKLY": "Weekly",
          "ACTIVITY_OPEN": "Event (Opening)",
          "ACTIVITY_CLOSED": "Event (Closed)"
        },
        "status": {
          "closed": "Closed",
          "open": "Opening"
        }
      },
      "stage": {
        "name": "Stage",
        "apCost": "{apCost} AP required",
        "loots": {
          "normal": "Normal",
          "extra": "Extra",
          "special": "Special"
        }
      },
      "stats": {
        "name": "Statistics",
        "title": "Statistics of {stage}"
      }
    },
    "ja": {
      "opensAt": "開催期間：{0} ~ {1}",
      "zone": {
        "name": "章",
        "types": {
          "MAINLINE": "メインストーリー",
          "WEEKLY": "曜日クエスト",
          "ACTIVITY_OPEN": "イベント（開催中）",
          "ACTIVITY_CLOSED": "イベント（終了）"
        },
        "status": {
          "closed": "終了",
          "open": "開催中"
        }
      },
      "stage": {
        "name": "作戦",
        "apCost": "消費理智：{apCost}",
        "loots": {
          "normal": "通常ドロップ",
          "extra": "エクストラドロップ",
          "special": "スペシャルドロップ"
        }
      },
      "stats": {
        "name": "統計結果",
        "title": "{stage} 統計結果"
      }
    }
  }
</i18n>

<template>
  <v-stepper
    v-model="step"
    :alt-labels="!$vuetify.breakpoint.xsOnly"
    class="bkop-light transparent"
  >
    <v-stepper-header>
      <v-stepper-step
        :complete="step > 1"
        :editable="step > 1"
        :step="1"
      >
        <v-layout
          column
          align-center
          justify-center
          wrap
          class="text-xs-center"
        >
          {{ $t('zone.name') }}
          <small v-if="step > 1">{{ selectedZone.zoneName }}</small>
        </v-layout>
      </v-stepper-step>

      <v-divider />

      <v-stepper-step
        :complete="step > 2"
        :editable="step > 2"
        :step="2"
      >
        <v-layout
          column
          align-center
          justify-center
          wrap
          class="text-xs-center"
        >
          {{ $t('stage.name') }}
          <small v-if="step > 2">{{ selectedStage.code }}</small>
        </v-layout>
      </v-stepper-step>

      <v-divider />

      <v-stepper-step
        :complete="step === 3"
        :step="3"
      >
        <v-layout
          column
          align-center
          justify-center
          wrap
          class="text-xs-center"
        >
          {{ $t('stats.name') }}
        </v-layout>
      </v-stepper-step>
    </v-stepper-header>

    <v-stepper-items>
      <v-stepper-content :step="1">
        <v-container
          fluid
          grid-list-lg
          class="py-0"
        >
          <v-list
            v-for="zoneCategory in categorizedZones"
            :key="zoneCategory.id"
            subheader
            class="transparent"
          >
            <v-subheader inset>
              {{ $t(['zone.types', zoneCategory.id].join('.')) }}
            </v-subheader>

            <v-list-tile
              v-for="zone in zoneCategory.zones"
              :key="zone.zoneId"
              v-ripple
              avatar
              @click="storeZoneSelection(zone.zoneId)"
            >
              <v-list-tile-avatar>
                <v-icon>{{ zone.icon }}</v-icon>
              </v-list-tile-avatar>

              <v-tooltip
                :disabled="!zone.isActivity"
                left
              >
                <template v-slot:activator="{ on }">
                  <v-list-tile-content
                    v-on="on"
                  >
                    <v-list-tile-title>{{ zone.zoneName }}</v-list-tile-title>
                    <v-list-tile-sub-title v-if="zone.isActivity">
                      <span
                        :class="{
                          'text--darken-1 font-weight-bold': true,
                          'red--text': zone.isOutdated,
                          'green--text': !zone.isOutdated }"
                      >
                        {{ zone.isOutdated ? $t('zone.status.closed') : $t('zone.status.open') }}
                      </span>
                    </v-list-tile-sub-title>
                  </v-list-tile-content>
                </template>
                {{ $t('opensAt', zone.activityActiveTime) }}
              </v-tooltip>

              <v-list-tile-action>
                <v-icon color="grey lighten-1">
                  mdi-chevron-right
                </v-icon>
              </v-list-tile-action>
            </v-list-tile>
          </v-list>
        </v-container>
      </v-stepper-content>

      <v-stepper-content
        v-if="selected.zone"
        :step="2"
      >
        <v-container
          fluid
          grid-list-lg
          class="py-0"
        >
          <v-list
            :three-line="$vuetify.breakpoint.smAndUp"
            subheader
            class="transparent"
          >
            <v-subheader
              v-if="selectedZone"
              inset
            >
              {{ selectedZone.zoneName || '' }}
            </v-subheader>

            <v-list-tile
              v-for="stage in stages"
              :key="stage.stageId"
              v-ripple
              avatar
              @click="storeStageSelection(stage.stageId)"
            >
              <v-list-tile-avatar>
                <v-icon>mdi-cube-outline</v-icon>
              </v-list-tile-avatar>

              <v-list-tile-content>
                <v-list-tile-title>{{ stage.code }}</v-list-tile-title>
                <v-list-tile-sub-title>
                  <v-layout
                    align-center
                    justify-start
                    row
                    wrap
                    d-inline-flex
                  >
                    <v-flex :class="{ 'yellow--text font-weight-bold': true, 'amber--text text--darken-4': !$vuetify.dark }">
                      {{ $t('stage.apCost', {apCost: stage.apCost}) }}
                    </v-flex>

                    <v-divider
                      v-if="stage.normalDrop.length > 0"
                      vertical
                      class="hidden-xs-only mx-1"
                    />

                    <v-flex
                      v-if="stage.normalDrop.length > 0"
                      class="hidden-xs-only"
                    >
                      <div>{{ $t('stage.loots.normal') }}</div>
                      <Item
                        v-for="item in stage.normalDrop"
                        :key="item"
                        :item="getItem(item)"
                        :ratio="0.6"
                        disable-link
                      />
                    </v-flex>

                    <v-divider
                      v-if="stage.extraDrop.length > 0"
                      vertical
                      class="hidden-sm-and-down mx-1"
                    />

                    <v-flex
                      v-if="stage.extraDrop.length > 0"
                      class="hidden-sm-and-down"
                    >
                      <div>{{ $t('stage.loots.extra') }}</div>
                      <Item
                        v-for="item in stage.extraDrop"
                        :key="item*10"
                        :item="getItem(item)"
                        :ratio="0.6"
                        disable-link
                      />
                    </v-flex>

                    <v-divider
                      v-if="stage.specialDrop.length > 0"
                      vertical
                      class="hidden-sm-and-down mx-1"
                    />

                    <v-flex
                      v-if="stage.specialDrop.length > 0"
                      class="hidden-sm-and-down"
                    >
                      <div>{{ $t('stage.loots.special') }}</div>
                      <Item
                        v-for="item in stage.specialDrop"
                        :key="item*100"
                        :item="getItem(item)"
                        :ratio="0.6"
                        disable-link
                      />
                    </v-flex>
                  </v-layout>
                </v-list-tile-sub-title>
              </v-list-tile-content>

              <v-list-tile-action>
                <v-icon color="grey lighten-1">
                  mdi-chevron-right
                </v-icon>
              </v-list-tile-action>
            </v-list-tile>
          </v-list>
        </v-container>
      </v-stepper-content>

      <v-stepper-content :step="3">
        <v-layout
          align-center
          justify-space-between
        >
          <h1 class="title ma-3">
            {{ $t('stats.title', {stage: selectedStage.code}) }}
          </h1>
          <DataSourceToggle />
        </v-layout>
        <v-data-table
          :headers="tableHeaders"
          :items="stageStats"
          :pagination.sync="tablePagination"
          :calculate-widths="true"
          must-sort
          hide-actions
          class="elevation-0 transparentTable stat-table"
        >
          <template v-slot:items="props">
            <tr>
              <td
                :class="{
                  'px-3': $vuetify.breakpoint.smAndDown,
                  'item-name-td-xs': $vuetify.breakpoint.xsOnly,
                  'item-name-td-sm': $vuetify.breakpoint.smOnly
                }"
                class="hovering"
              >
                <span
                  class="cursor-pointer"
                  @click="redirectItem(props.item.item.itemId)"
                >
                  <v-hover>
                    <span
                      slot-scope="{ hover }"
                      :class="{
                        'hovering--hovered': hover
                      }"
                    >
                      <v-avatar
                        :size="30"
                        class="mr-1"
                      >
                        <Item
                          :item="props.item.item"
                          :ratio="0.65"
                          disable-tooltip
                          disable-link
                        />
                      </v-avatar>
                      <span
                        v-if="!$vuetify.breakpoint.xsOnly"
                        class="ml-2"
                      >
                        {{ props.item.item.name }}
                      </span>
                      <v-slide-x-transition>
                        <v-icon
                          v-if="hover || $vuetify.breakpoint.smOnly"
                          small
                        >mdi-chevron-right</v-icon>
                      </v-slide-x-transition>
                    </span>
                  </v-hover>
                </span>
              </td>
              <td
                :class="{'px-3': $vuetify.breakpoint.xsOnly}"
                class="text-xs-center"
              >
                {{ props.item.times }}
              </td>
              <td
                :class="{'px-3': $vuetify.breakpoint.xsOnly}"
                class="text-xs-center"
              >
                {{ props.item.quantity }}
              </td>
              <td
                :class="{'px-3': $vuetify.breakpoint.xsOnly}"
                class="text-xs-center"
              >
                <div
                  class="charts-data-wrapper"
                  fill-height
                >
                  {{ props.item.percentageText }}
                  <div
                    class="charts-wrapper cursor-pointer"
                    fill-height
                  >
                    <Charts
                      v-if="currentTrends"
                      :interval="currentTrends && currentTrends.interval"
                      :x-start="currentTrends && currentTrends.startTime"
                      :show-dialog="expanded[props.item.item.itemId]"
                      :data-keys="['quantity']"
                      :data="currentTrendsData && currentTrendsData[props.item.item.itemId]"
                      :charts-id="props.item.item.itemId"
                      sparkline-key="quantity"
                      sparkline-sub-key="times"
                    />
                  </div>
                </div>
              </td>
              <td
                :class="{'px-3': $vuetify.breakpoint.xsOnly}"
                class="text-xs-center"
              >
                {{ props.item.apPPR }}
              </td>
            </tr>
          </template>
        </v-data-table>
      </v-stepper-content>
    </v-stepper-items>
  </v-stepper>
</template>

<script>
import get from "@/utils/getters";
import Item from "@/components/Item";
import Charts from "@/components/Charts";
import DataSourceToggle from "@/components/DataSourceToggle";

export default {
  name: "StatsByStage",
  components: { Item, Charts, DataSourceToggle },
  data: () => ({
    expanded: {},
    step: 1,
    tablePagination: {
      rowsPerPage: -1,
      sortBy: "percentage",
      descending: true
    }
  }),
  computed: {
    selected() {
      return {
        zone: this.$route.params.zoneId,
        stage: this.$route.params.stageId
      };
    },
    currentTrends() {
      return get.trends.byStageId(this.$route.params.stageId);
    },
    currentTrendsData() {
      return this.currentTrends && this.currentTrends.results;
    },
    categorizedZones() {
      const categories = ["ACTIVITY_OPEN", "MAINLINE", "WEEKLY", "ACTIVITY_CLOSED"];
      let result = [];
      for (let category of categories) {
        let filter = null;
        let zones = get.zones.byType(category.startsWith("ACTIVITY") ? "ACTIVITY" : category);
        if (category === "ACTIVITY_OPEN") {
          filter = zone => !zone.isOutdated;
        } else if (category === "ACTIVITY_CLOSED") {
          filter = zone => zone.isOutdated;
        }
        if (filter) {
          zones = zones.filter(filter);
        }
        if (zones && zones.length) {
          result.push({
            id: category,
            zones: zones
          })
        }
      }
      return result;
    },
    selectedZone() {
      if (!this.selected.zone) return { zoneName: "" };
      return get.zones.byZoneId(this.selected.zone);
    },
    selectedStage() {
      if (!this.selected.stage) return {};
      return get.stages.byStageId(this.selected.stage);
    },
    stages() {
      return get.stages.byParentZoneId(this.selected.zone);
    },
    stageStats() {
      if (!this.selected.stage) return [];
      return get.statistics.byStageId(this.selected.stage);
    },
    tableHeaders() {
      return [
        {
          text: this.$t("stats.headers.item"),
          value: "icon",
          align: "center",
          sortable: false,
          width: "250px"
        },
        {
          text: this.$t("stats.headers.times"),
          value: "times",
          align: "center",
          sortable: true
        },
        {
          text: this.$t("stats.headers.quantity"),
          value: "quantity",
          align: "center",
          sortable: true
        },
        {
          text: this.$t("stats.headers.percentage"),
          value: "percentage",
          align: "center",
          sortable: true
        },
        {
          text: this.$t("stats.headers.apPPR"),
          value: "apPPR",
          align: "center",
          sortable: true
        }
      ];
    }
  },
  watch: {
    $route: function(to, from) {
      console.log("step route changed from", from.path, "to", to.path);
      if (to.name === "StatsByStage") {
        this.step = 1;
      }
      if (to.name === "StatsByStage_SelectedZone") {
        this.step = 2;
      }
      if (to.name === "StatsByStage_SelectedBoth") {
        this.step = 3;
      }
    },
    step: function(newValue, oldValue) {
      console.log("step changed from", oldValue, "to", newValue);
      switch (newValue) {
        case 1:
          console.log("- [router go] index");
          this.$router.push({ name: "StatsByStage" });
          break;
        case 2:
          console.log("- [router go] zone", this.selected.zone);
          this.$router.push({
            name: "StatsByStage_SelectedZone",
            params: { zoneId: this.selected.zone }
          });
          break;
        case 3:
          console.log("- [router go] stage", this.selected);
          this.$router.push({
            name: "StatsByStage_SelectedBoth",
            params: {
              zoneId: this.selected.zone,
              stageId: this.selected.stage
            }
          });
          break;
        default:
          console.error(
            "unexpected step number",
            newValue,
            "with [newStep, oldStep]",
            [newValue, oldValue]
          );
      }
    }
  },
  beforeMount() {
    this.$route.params.zoneId &&
      (this.selected.zone = this.$route.params.zoneId) &&
      (this.step += 1);
    this.$route.params.stageId &&
      (this.selected.stage = this.$route.params.stageId) &&
      (this.step += 1);
  },
  methods: {
    redirectItem(itemId) {
      this.$router.push({
        name: "StatsByItem_SelectedItem",
        params: {
          itemId
        }
      });
    },
    storeZoneSelection(zoneId) {
      this.$router.push({
        name: "StatsByStage_SelectedZone",
        params: { zoneId: zoneId }
      });
    },
    storeStageSelection(stageId) {
      this.$router.push({
        name: "StatsByStage_SelectedBoth",
        params: {
          zoneId: this.selected.zone,
          stageId: stageId
        }
      });
    },
    getItem(itemId) {
      return get.item.byItemId(itemId);
    }
  }
};
</script>

<style scoped>
.theme--light .zoneTitle {
  color: #fff;
}

.v-table {
  background: transparent !important;
}

.charts-data-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
}

.charts-wrapper {
  display: flex;
  align-items: center;
}

::v-deep .stat-table th {
  padding-left: 8px !important;
  padding-right: 8px !important;
}

.item-name-td-xs {
  min-width: 100px;
}

.item-name-td-sm {
  min-width: 160px;
}

::v-deep .stat-table th i {
  margin-left: -16px;
}
</style>
