<template>
  <v-container>
    <v-row>
      <v-col cols="12">
        <h1 class="text-center">ALARM-A-DORE SETTINGS</h1>
      </v-col>
      <v-col cols="3" />
      <v-col cols="6">
        <v-table>
          <thead>
            <tr>
              <th>YOUR RINGY THINGY</th>
              <th>TEST-A-DING</th>
              <th>PICK-A-DING</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="alarm in settings.alarms" :key="alarm.id">
              <td>{{ alarm.name }}</td>
              <td>
                <!-- controls 加上播放控制按鈕 -->
                <audio controls :src="alarm.file" />
              </td>
              <td>
                <!-- hide-details 把多餘佔空間的 messages 刪掉，才可以垂直置中 -->
                <v-radio-group v-model="settings.selected" hide-details>
                  <v-radio :value="alarm.id" />
                </v-radio-group>
              </td>
            </tr>
          </tbody>
        </v-table>
      </v-col>
      <v-col cols="3" />
    </v-row>
  </v-container>
</template>

<script setup>
import { useSettingsStore } from '@/stores/settings'

const settings = useSettingsStore()
</script>
