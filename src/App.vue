<template>
  <div class="container">
    <div class="building">
      <div class="floor"></div>
      <div class="floor"></div>
      <div class="floor"></div>
      <div class="floor"></div>
      <div class="floor"></div>
      <div class="elevator" :style="{ bottom: value + '%' }">
        <div class="door-left" :style="{ right: doorPosition + '%' }"></div>
        <div class="door-right" :style="{ left: doorPosition + '%' }"></div>
      </div>
    </div>
    <div class="number">
      <div class="floor">
        <h2>5</h2>
        <button @click="click(5)">
          <div class="round" :style="{ background: roundColor[4] }"></div>
        </button>
      </div>
      <div class="floor">
        <h2>4</h2>
        <button @click="click(4)">
          <div class="round" :style="{ background: roundColor[3] }"></div>
        </button>
      </div>
      <div class="floor">
        <h2>3</h2>
        <button @click="click(3)">
          <div class="round" :style="{ background: roundColor[2] }"></div>
        </button>
      </div>
      <div class="floor">
        <h2>2</h2>
        <button @click="click(2)">
          <div class="round" :style="{ background: roundColor[1] }"></div>
        </button>
      </div>
      <div class="floor">
        <h2>1</h2>
        <button @click="click(1)">
          <div class="round" :style="{ background: roundColor[0] }"></div>
        </button>
      </div>
    </div>
    <div class="indicator">
      <div class="top" :style="{ background: bgtop }"></div>
      <div class="tablo">{{ nextFloor }}</div>
      <div class="bottom" :style="{ background: bgbottom }"></div>
    </div>
  </div>
</template>

<script>
const MOVE='move'
const PEACE = "peace";
const BUSY = "busy";

export default {
  name: "App",
  data() {
    return {
      value: 20,
      state: PEACE,
      arr: [],
      nextFloor: null,
      doorPosition: 0,
      openDoorIntervalId: 0,
      intervalUpid: 0,
      intervalDownid: 0,
      bgtop: "white",
      bgbottom: "white",
      roundColor: ["black", "black", "black", "black", "black"],
    };
  },
  methods: {
    openDoor(floorNumber) {
      this.state = BUSY;
      let openInterval = setInterval(() => {
        this.doorPosition += 6;
      }, 170);
      setTimeout(() => {
        clearInterval(openInterval);
      }, 1000);
      setTimeout(() => {
        let closeInterval = setInterval(() => {
          this.doorPosition -= 6;
        }, 170);
        setTimeout(() => clearInterval(closeInterval), 1000);
      }, 2000);
      setTimeout(() => {
        if (!this.arr.length) {
          this.state = PEACE;
        } else {
          this.nextFloor = this.arr.shift();
          if (this.value < this.nextFloor * 20) {
            this.moveUp(this.nextFloor);
          } else if (this.value > this.nextFloor * 20) {
            this.moveDown(this.nextFloor);
          }
        }
        clearInterval(this.openDoorIntervalId);
      }, 3000);
      this.indicator("off", floorNumber);
    },
    moveUp(floorNumber) {
      this.bgtop = "";
      this.state = MOVE;
      clearInterval(this.openDoorIntervalId);
      this.intervalUpid = setInterval(() => {
        this.value += 1;
        if (this.value === floorNumber * 20) {
          this.stop(floorNumber);
        }
      }, 50);
    },
    moveDown(floorNumber) {
      this.bgbottom = "";
      this.state = MOVE;
      clearInterval(this.openDoorIntervalId);
      this.intervalDownid = setInterval(() => {
        this.value -= 1;
        if (this.value === floorNumber * 20) {
          this.stop(floorNumber);
        }
      }, 50);
    },
    stop(floorNumber) {
      this.indicator("off", floorNumber);
      clearInterval(this.intervalUpid);
      clearInterval(this.intervalDownid);
      this.openDoor(floorNumber);
      this.bgtop = "white";
      this.bgbottom = "white";
    },
    callElevator(floorNumber) {
      if (
        !this.arr.includes(floorNumber) &&
        this.value != floorNumber * 20 &&
        this.nextFloor != floorNumber
      )
        this.arr.push(floorNumber);
    },
    click(floorNumber) {
      this.indicator("on", floorNumber);
      switch (this.state) {
        case PEACE:
          this.nextFloor = floorNumber;
          if (this.value < floorNumber * 20) {
            this.moveUp(floorNumber);
          } else if (this.value > floorNumber * 20) {
            this.moveDown(floorNumber);
          }
          break;
        case BUSY:
          this.callElevator(floorNumber);
          break;
      }
    },
    indicator(type, floorNumber) {
      if (type == "on") {
        switch (floorNumber) {
          case 1:
            this.roundColor[0] = "red";
            break;
          case 2:
            this.roundColor[1] = "red";
            break;
          case 3:
            this.roundColor[2] = "red";
            break;
          case 4:
            this.roundColor[3] = "red";
            break;
          case 5:
            this.roundColor[4] = "red";
            break;
        }
      } else {
        switch (floorNumber) {
          case 1:
            this.roundColor[0] = "black";
            break;
          case 2:
            this.roundColor[1] = "black";
            break;
          case 3:
            this.roundColor[2] = "black";
            break;
          case 4:
            this.roundColor[3] = "black";
            break;
          case 5:
            this.roundColor[4] = "black";
            break;
        }
      }
    },
  },
};
</script>
<style>
#app {
  height: 850px;
  width: 100%;
}
.container {
  height: 100%;
  width: 100%;
  box-sizing: border-box;
}
.building {
  height: 100%;
  width: 10%;
  box-sizing: border-box;
  float: left;
  border: 1px solid black;
}
.floor {
  height: 20%;
  margin-left: 100px;
  width: 30%;
  box-sizing: border-box;
  text-align: center;
}
.elevator {
  background-image: url("./assets/elevator.svg");
  background-size: contain;
  background-repeat: no-repeat;
  background-position: center;
  position: relative;
  height: 20%;
  width: 100%;
  z-index: 2;
  bottom: 20%;
  overflow: hidden;
  text-align: center;
}
.door-right {
  height: 100%;
  width: 50%;
  box-sizing: border-box;
  background-image: url("./assets/door-right.svg");
  background-size: cover;
  float: left;
  position: relative;
  z-index: 0;
  left: 50%;
}
.door-left {
  height: 100%;
  width: 50%;
  box-sizing: border-box;
  background-image: url("./assets/door-left.svg");
  background-size: cover;
  float: left;
  position: relative;
  z-index: 0;
  right: 50%;
}
.number {
  height: 100%;
  width: 20%;
  box-sizing: border-box;
  float: left;
}
button {
  width: 40px;
  height: 40px;
  border: none;
  background: darkgray;
  border-radius: 50%;
  padding: 16px 16px;
}
.indicator {
  box-sizing: border-box;
  float: left;
  height: 100%;
  width: 20%;
}
.tablo {
  box-sizing: border-box;
  height: 10%;
  text-align: center;
  font-size: 48px;
  font-weight: bold;
}
.top {
  box-sizing: border-box;
  height: 10%;
  background-image: url("./assets/Arrowtop.svg");
  background-size: contain;
  background-repeat: no-repeat;
  background-position: center;
}
.bottom {
  box-sizing: border-box;
  height: 10%;
  background-image: url("./assets/Arrowbottom.svg");
  background-size: contain;
  background-repeat: no-repeat;
  background-position: center;
}
.round {
  background: black;
  height: 8px;
  width: 8px;
  border-radius: 50%;
}

</style>
