<template>
  <div>
    <p>{{ time }}</p>
  </div>
</template>

<script>
export default {
  props: ["running"],
  data: function () {
    return {
      time: "00:00:00.000",
      interval: undefined,
    };
  },
  created() {
    let offset;
    let clock = 0;

    const update = () => {
      clock += delta();
      console.log(clock);
      this.time = `${Date(clock / 1000)}`;
    };

    if (!this.interval) {
      offset = Date.now();
      this.interval = setInterval(update, 500);
    }

    function delta() {
      let now = Date.now();
      const d = now - offset;
      offset = now;
      return d;
    }
  },
};
</script>