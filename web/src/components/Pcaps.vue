<template>
  <div>
    <v-data-table
      :headers="headers"
      :items="pcapsPretty"
      :loading="pcaps === null"
      disable-pagination
      hide-default-footer
      dense
    >
      <template
        v-for="field of [
          'ParseTime',
          'PacketTimestampMin',
          'PacketTimestampMax',
        ]"
        #[`item.${field}`]="{ index, value }"
        ><span :key="`${field}/${index}`" :title="value | formatDateLong">{{
          value | formatDate
        }}</span></template
      >
      <template #[`item.Filesize`]="{ value }"
        ><span :title="`${value} Bytes`">{{
          value | prettyBytes(1, true)
        }}</span></template
      >
    </v-data-table>
  </div>
</template>

<script>
import { mapActions, mapState } from "vuex";

export default {
  name: "Pcaps",
  data() {
    return {
      headers: [
        {
          text: "File Name",
          value: "Filename",
        },
        {
          text: "First Packet Time",
          value: "PacketTimestampMin",
        },
        {
          text: "Last Packet Time",
          value: "PacketTimestampMax",
        },
        {
          text: "Packet Count",
          value: "PacketCount",
        },
        {
          text: "File Size",
          value: "Filesize",
        },
        {
          text: "Parse Time",
          value: "ParseTime",
        },
      ],
    };
  },
  computed: {
    ...mapState(["pcaps"]),
    pcapsPretty() {
      if (this.pcaps == null) return [];
      return this.pcaps.map((i) => {
        const res = { ...i };
        for (const k of [
          "ParseTime",
          "PacketTimestampMin",
          "PacketTimestampMax",
        ]) {
          let v = new Date(res[k]);
          if (v < 0) v = null;
          res[k] = v;
        }
        return res;
      });
    },
  },
  mounted() {
    this.updatePcaps();
  },
  methods: {
    ...mapActions(["updatePcaps"]),
  },
};
</script>
