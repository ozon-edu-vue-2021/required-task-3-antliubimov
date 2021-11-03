<template>
  <div class="map">
    <h3>Карта офиса</h3>

    <div v-if="!isLoading" class="map-root">
      <MapSVG ref="svg" />
      <Table v-show="false" ref="table" v-click-outside="hide" />
    </div>
    <div v-else>Loading...</div>
  </div>
</template>

<script>
import MapSVG from "@/assets/images/map.svg";
import Table from "@/assets/images/workPlace.svg";
import tables from "@/assets/data/tables.json";
import legend from "@/assets/data/legend.json";
import * as d3 from "d3";
import ClickOutside from "vue-click-outside";

export default {
  props: {
    isUserOpenned: {
      type: Boolean,
      default: false,
    },
  },
  components: {
    MapSVG,
    Table,
  },
  data() {
    return {
      isLoading: false,
      svg: null,
      g: null,
      tableSVG: null,
      tables: [],
      currId: null,
    };
  },
  created() {
    this.tables = tables;
  },
  mounted() {
    this.isLoading = true;

    this.svg = d3.select(this.$refs.svg);
    this.g = this.svg.select("g");
    this.tableSVG = d3.select(this.$refs.table);

    if (this.g) {
      this.drawTables();
    } else {
      alert("SVG is incorrect!");
    }

    this.isLoading = false;
  },
  methods: {
    drawTables() {
      // создаем группу для рабочих столов
      const svgTablesGroupPlace = this.g
        .append("g")
        .classed("groupPlaces", true);

      this.tables.map((table) => {
        // создает группу для рабочего стола
        const targetSeat = svgTablesGroupPlace
          .append("g")
          .attr("transform", `translate(${table.x}, ${table.y}) scale(0.5)`)
          .attr("id", table._id)
          .classed("employer-place", true)
          .on("click", this.showPersonCard);

        // устанавливает стол в группу
        targetSeat
          .append("g")
          .attr("transform", `rotate(${table.rotate || 0})`)
          .attr("group_id", table.group_id)
          .classed("table", true)
          .html(this.tableSVG.html())
          .attr(
            // устанавливаем цвет подразделения
            "fill",
            legend.find((it) => it.group_id === table.group_id)?.color ??
              "transparent"
          );
      });
    },
    showPersonCard(e) {
      this.currId = e.target.parentNode.parentNode.parentNode.id;
      this.$emit("show-person-card", this.currId);
    },
    hide(e) {
      if (e.target.parentNode.parentNode.parentNode.id !== this.currId) {
        if (this.isUserOpenned) {
          this.$emit("update:isUserOpenned", false);
        }
      }
    },
  },
  directives: {
    ClickOutside,
  },
};
</script>

<style scoped>
.map {
  height: 100%;
  width: 100%;
  padding: 24px;
  overflow: hidden;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
}

.map-root {
  height: 100%;
  width: 100%;
  overflow: hidden;
  box-sizing: border-box;
}

h3 {
  margin-top: 0px;
}

::v-deep svg {
  height: 100%;
  width: 100%;
}

::v-deep .table {
  cursor: pointer;
}
</style>
