<script>
  import { onMount } from 'svelte';
  let mTime = '0 min', bkTime = '0 min', conn, btn

  onMount(() => {
    console.log('WS')
    conn = new WebSocket('ws://mta.tony.place/ws?stopId=L12&subwayLine=L')
    //wss://mta.tony.place/ws?stopId=L12
    btn = document.getElementById('btn')

    //conn.addEventListener("open", (evt) => {
    //  console.log("open", conn.readyState);
    //  conn.send(JSON.stringify({"ready": true}))
    //})

    //conn.addEventListener("message", (evt) => {
      conn.onmessage = function(evt) {
      var messages = evt.data.split('\n')
      let data = JSON.parse(messages)
      console.log(data);
      const { manhattan, brooklyn } = parse(data)
      console.log(manhattan)
      mTime = manhattan[0]?.train.timeInMinutes != undefined ? manhattan[0]?.train.timeInMinutes + ' min' : 0 + ' min'
      bkTime = brooklyn[0]?.train.timeInMinutes != undefined ? brooklyn[0]?.train.timeInMinutes + ' min' : 0 + " min"
    }//)




    btn.addEventListener('click', async function () {
      console.log('Click')

      try {
        const res = await fetch('https://mta.tony.place/transit', {
          method: 'POST'
        })
        const data = await res.json()
        console.log(parse(data))
      } catch (error) {
        console.error(error)
      }

    })
  })

  //conn.send(
  //  JSON.stringify({
  //    id: 'IDDD',
  //    test: 'TEST'
  //  })
  //)

function parse(data) {
  let brooklyn = data.trains
    .filter((train) => train.direction === 'Brooklyn')
    .sort((a, b) => a.train.arrivalTimeDelay - b.train.arrivalTimeDelay)
    .filter((train) => {
      //const now = Math.floor(new Date().getTime() / 1000)
      return !(train.train.arrivalTimeDelay <= Date.now() / 1000)
    })
  let manhattan = data.trains
    .filter((train) => train.direction === 'Manhattan')
    .sort((a, b) => a.train.arrivalTimeDelay - b.train.arrivalTimeDelay)
    .filter((train) => {
      //const now = Math.floor(new Date().getTime() / 1000)
      return !(train.train.arrivalTimeDelay <= Date.now() / 1000)
    })

  return { manhattan, brooklyn }
}
</script>
  <div class='outer-border'>
    <div class='inner-border'>
      <div class='inner'>
        <div class='top'>
          <span class='base-text'> 1.</span>
          <div class='svg-container'>
            <svg
              viewBox='0 0 24 25'
              version='1.1'
              xmlns='http://www.w3.org/2000/svg'
              xmlns:xlink='http://www.w3.org/1999/xlink'
            >
              <!-- Generator: Sketch 47 (45396) - http://www.bohemiancoding.com/sketch -->
              <title>L</title>
              <desc>Created with Sketch.</desc>
              <defs></defs>
              <g
                id='Page-1'
                stroke='none'
                stroke-width='1'
                fill='none'
                fill-rule='evenodd'
              >
                <g
                  id='Modes-of-transport-and-lines'
                  transform='translate(-171.000000, -1216.000000)'
                >
                  <g id='L' transform='translate(171.000000, 1216.000000)'>
                    <path
                      d='M0.0004,12.6993 C0.0004,6.0713 5.3724,0.6993 12.0004,0.6993 C18.6274,0.6993 24.0004,6.0713 24.0004,12.6993 C24.0004,19.3263 18.6274,24.6993 12.0004,24.6993 C5.3724,24.6993 0.0004,19.3263 0.0004,12.6993'
                      id='Fill-96'
                      fill='#A7A9AC'></path>
                    <polygon
                      id='Fill-97'
                      fill='#FFFFFF'
                      points='8.0209 18.3575 8.0209 7.0415 10.2279 7.0415 10.2279 16.4865 15.9779 16.4865 15.9779 18.3575'
                    ></polygon>
                  </g>
                </g>
              </g>
            </svg>
          </div>
          <span class='base-text'> Manhattan</span>
          <span class='base-text time' id='m-time'>{mTime}</span>
        </div>
        <span class='spacer'></span>
        <div class='bottom'>
          <span class='base-text'> 2.</span>
          <div class='svg-container'>
            <svg
              viewBox='0 0 24 25'
              version='1.1'
              xmlns='http://www.w3.org/2000/svg'
              xmlns:xlink='http://www.w3.org/1999/xlink'
            >
              <!-- Generator: Sketch 47 (45396) - http://www.bohemiancoding.com/sketch -->
              <title>L</title>
              <desc>Created with Sketch.</desc>
              <defs></defs>
              <g
                id='Page-1'
                stroke='none'
                stroke-width='1'
                fill='none'
                fill-rule='evenodd'
              >
                <g
                  id='Modes-of-transport-and-lines'
                  transform='translate(-171.000000, -1216.000000)'
                >
                  <g id='L' transform='translate(171.000000, 1216.000000)'>
                    <path
                      d='M0.0004,12.6993 C0.0004,6.0713 5.3724,0.6993 12.0004,0.6993 C18.6274,0.6993 24.0004,6.0713 24.0004,12.6993 C24.0004,19.3263 18.6274,24.6993 12.0004,24.6993 C5.3724,24.6993 0.0004,19.3263 0.0004,12.6993'
                      id='Fill-96'
                      fill='#A7A9AC'></path>
                    <polygon
                      id='Fill-97'
                      fill='#FFFFFF'
                      points='8.0209 18.3575 8.0209 7.0415 10.2279 7.0415 10.2279 16.4865 15.9779 16.4865 15.9779 18.3575'
                    ></polygon>
                  </g>
                </g>
              </g>
            </svg>
          </div>
          <span class='base-text'> Brooklyn</span>
          <span class='base-text time' id='b-time'>{bkTime}</span>
        </div>
      </div>
    </div>
  </div>
  <button id='btn'>send</button>
<style>
  * {
    box-sizing: content-box;
  }

  .outer-border {
    width: 500px;
    height: 150px;
    margin: 100px auto;
    border: 15px solid #2e2d2d;
    border-radius: 10px;
    display: flex;
    justify-content: center;
  }

  .inner-border {
    width: 100%;
    height: 100%;
    border: 1px solid #242424;
    /*     //#242424; */
    background: black;
    align-self: center;
    z-index: -1;
    display: flex;
    justify-content: center;
  }

  .inner {
    width: 98%;
    height: 95%;
    background: white;
    align-self: center;
    z-index: 1;
  }

  .top {
    height: 50%;
    padding-left: 8px;
    padding-right: 8px;
    align-items: flex-end;
    display: flex;
    gap: 16px;
  }

  .bottom {
    height: 50%;
    padding-left: 8px;
    padding-right: 8px;
    align-items: flex-end;
    display: flex;
    gap: 16px;
  }

  .spacer {
    display: flex;
    border: 0.5px solid grey;
  }

  .base-text {
    font-family: 'Open Sans', sans-serif;
    font-size: 40px;
    padding-bottom: 8px;
  }

  .svg-container {
    display: inline-block;
    width: 50px;
    padding-bottom: 8px;
  }

  svg {
    /*width: 100%;*/
  }

  .time {
    flex-grow: 1;
    text-align: right;
  }

  @media only screen and (max-width: 767px) {
    .outer-border {
      max-width: 300px;
      width: 100%;
      height: 100px;
    }

    .base-text {
      font-size: 24px;
      padding-bottom: 4px;
    }

    .svg-container {
      width: 25px;
      padding-bottom: 4px;
    }

    .top {
      gap: 8px;
    }

    .bottom {
      gap: 8px;
    }
  }
</style>
