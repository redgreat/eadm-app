<template>
  <view class="container">
    <map
      class="map"
      :latitude="centerLatitude"
      :longitude="centerLongitude"
      :polyline="polyline"
      :markers="markers"
      show-location
    ></map>
  </view>
</template>

<script>
export default {
  data() {
    return {
      centerLatitude: 39.908823,
      centerLongitude: 116.397470,
      polyline: [],
      markers: []
    }
  },
  onLoad() {
    // 检查登录状态
    const token = uni.getStorageSync('token')
    if (!token) {
      uni.reLaunch({
        url: '/pages/login/index'
      })
      return
    }

    this.fetchTrackData()
  },
  methods: {
    async fetchTrackData() {
      try {
        const token = uni.getStorageSync('token')
        const response = await uni.request({
          url: 'YOUR_TRACK_API_URL',
          method: 'GET',
          header: {
            'Authorization': `Bearer ${token}`
          }
        })

        if (response.data.success) {
          const trackPoints = response.data.trackPoints
          if (trackPoints && trackPoints.length > 0) {
            // 设置地图中心点为轨迹的第一个点
            this.centerLatitude = trackPoints[0].latitude
            this.centerLongitude = trackPoints[0].longitude

            // 设置轨迹线
            this.polyline = [{
              points: trackPoints.map(point => ({
                latitude: point.latitude,
                longitude: point.longitude
              })),
              color: '#007AFF',
              width: 4
            }]

            // 设置起点和终点标记
            this.markers = [
              {
                id: 1,
                latitude: trackPoints[0].latitude,
                longitude: trackPoints[0].longitude,
                title: '起点',
                iconPath: '/static/start.png',
                width: 30,
                height: 30
              },
              {
                id: 2,
                latitude: trackPoints[trackPoints.length - 1].latitude,
                longitude: trackPoints[trackPoints.length - 1].longitude,
                title: '终点',
                iconPath: '/static/end.png',
                width: 30,
                height: 30
              }
            ]
          }
        } else {
          uni.showToast({
            title: response.data.message || '获取轨迹数据失败',
            icon: 'none'
          })
        }
      } catch (error) {
        uni.showToast({
          title: '获取轨迹数据失败，请稍后重试',
          icon: 'none'
        })
      }
    }
  }
}
</script>

<style>
.container {
  width: 100%;
  height: 100vh;
}

.map {
  width: 100%;
  height: 100%;
}
</style>
