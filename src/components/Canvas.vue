<template>
  <v-container>
    <v-row justify="center">
      <v-col cols="12" md="8">
        <v-card class="pa-4">
          <v-card-title>
            <p class="text-h5 text-center py-2">好きな惣菜発表ドラゴン太郎</p>
          </v-card-title>
          <v-card-text>
            <v-textarea
              v-model="text"
              rows="3"
              variant="outlined"
              label="ここにテキストを入力"
            ></v-textarea>
            <v-select
              v-model="fontSize"
              :items="[8, 16, 32, 48, 64]"
              label="フォントサイズ"
              class="my-4"
            ></v-select>
            <v-row justify="center">
              <v-btn @click="downloadImage" color="black" class="my-2">
                画像をダウンロード
              </v-btn>
            </v-row>
            <v-card class="my-4" flat>
              <div class="canvas-container">
                <canvas ref="canvas" width="1200" height="777"></canvas>
              </div>
            </v-card>
          </v-card-text>
        </v-card>
        <v-row justify="center" class="my-2">
          <v-col cols="12">
            <p class="text-center text-body-2">
              素材元:
              <a
                href="https://x.com/suck_a_sage/status/1793579307203440808"
                target="_blank"
              >
                https://x.com/suck_a_sage/status/1793579307203440808 </a
              ><br />
              <a href="https://x.com/suck_a_sage" target="_brank">
                @suck_a_sage </a
              >の許可をとって使用しています。
            </p>
          </v-col>
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup lang="ts">
import { ref, watch, onMounted } from "vue";

const text = ref<string>("");
const fontSize = ref<number>(30);
const canvas = ref<HTMLCanvasElement | null>(null);

const drawText = () => {
  if (!canvas.value) return;
  const ctx = canvas.value.getContext("2d");
  const image = new Image();
  image.src = "/template.jpeg";

  image.onload = () => {
    if (ctx) {
      ctx.clearRect(0, 0, canvas.value.width, canvas.value.height);
      ctx.drawImage(image, 0, 0, canvas.value.width, canvas.value.height);
      ctx.font = `${fontSize.value}px 'arial'`;
      ctx.fillStyle = "black";
      const textAreaX = 117;
      const textAreaY = 258;
      const textAreaWidth = 553 - 117;
      const textAreaHeight = 560 - 258;
      const maxWidth = textAreaWidth;
      const x = textAreaX;
      const y = textAreaY + fontSize.value;

      wrapText(
        ctx,
        text.value,
        x,
        y,
        maxWidth,
        fontSize.value,
        textAreaWidth,
        textAreaHeight
      );
    }
  };
};

const wrapText = (
  ctx: CanvasRenderingContext2D,
  text: string,
  x: number,
  y: number,
  maxWidth: number,
  lineHeight: number,
  textAreaWidth: number,
  textAreaHeight: number
) => {
  const lines = text.split("\n");
  let allLines = [];

  for (let i = 0; i < lines.length; i++) {
    let line = "";
    const words = lines[i].split(" ");
    for (let n = 0; n < words.length; n++) {
      const testLine = line + words[n] + " ";
      const metrics = ctx.measureText(testLine);
      const testWidth = metrics.width;
      if (testWidth > maxWidth && n > 0) {
        allLines.push(line);
        line = words[n] + " ";
      } else {
        line = testLine;
      }
    }
    allLines.push(line);
  }

  const totalTextHeight = allLines.length * lineHeight;
  let startY = y + (textAreaHeight - totalTextHeight) / 2;

  for (let line of allLines) {
    const metrics = ctx.measureText(line);
    const testWidth = metrics.width;
    ctx.fillText(line, x + (textAreaWidth - testWidth) / 2, startY);
    startY += lineHeight;
  }
};

const downloadImage = () => {
  if (!canvas.value) return;
  const link = document.createElement("a");
  link.href = canvas.value.toDataURL("image/png");
  link.download = "dragon-taro.png";
  link.click();
};

onMounted(() => {
  drawText();
});

watch([text, fontSize], drawText);
</script>

<style scoped>
.canvas-container {
  width: 100%;
  overflow: hidden;
}

canvas {
  width: 100%;
  height: auto;
  border: 1px solid black;
}

.text-center {
  text-align: center;
  font-weight: bold;
}
</style>
