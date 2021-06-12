<template>
<div class="min-h-screen bg-gray-100 py-6 flex flex-col justify-center sm:py-12">
  <div class="relative py-3 sm:mx-auto">
    <div class="absolute inset-0 bg-gradient-to-r from-cyan-400 to-light-blue-500 shadow-lg transform -skew-y-6 sm:skew-y-0 sm:-rotate-6 sm:rounded-3xl"></div>
    <div class="relative px-4 py-10 bg-white shadow-lg sm:rounded-3xl sm:p-20">
      <div class="max-w-md mx-auto">
        <div class="divide-y divide-gray-200">
          <div class="pb-8 text-lg leading-5 font-bold sm:text-xl sm:leading-6">
            <h1>QR Code Reader for SESAME</h1>
          </div>
          <div class="py-8 text-base leading-6 space-y-4 text-gray-700 sm:text-lg sm:leading-7">
            <p>Upload your QR code.</p>
            <p>All the processing is done in your browser, so your key's safe. You can even use me in Airplane mode.</p>
            <qrcode-capture @decode="onDecode" />
          </div>
          <div class="py-8 text-base leading-6 space-y-4 text-gray-700 sm:text-lg sm:leading-7" v-if="done">
            <ul class="list-disc space-y-2">
              <li class="flex items-start">
                <span class="h-6 flex items-center sm:h-7">
                  <svg class="flex-shrink-0 h-5 w-5 text-cyan-500" viewBox="0 0 20 20" fill="currentColor">
                    <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clip-rule="evenodd" />
                  </svg>
                </span>
                <p class="ml-2">
                  Device UUID: <code class="text-sm font-bold text-gray-900">{{ uuid }}</code>
                </p>
              </li>
              <li class="flex items-start">
                <span class="h-6 flex items-center sm:h-7">
                  <svg class="flex-shrink-0 h-5 w-5 text-cyan-500" viewBox="0 0 20 20" fill="currentColor">
                    <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clip-rule="evenodd" />
                  </svg>
                </span>
                <p class="ml-2">
                  Secret Key: <code class="text-sm font-bold text-gray-900">{{ secret }}</code>
                </p>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
</template>
<script>
    import * as base64 from "byte-base64";

    export default {
      data() {
        return {
          secret: '',
          uuid: '',
          done: false
        }
      },
      methods: {
        onDecode(result) {
          const urlParams = new URLSearchParams(result);
          const sk = urlParams.get('sk');
          const data = base64.base64ToBytes(sk);

          this.secret = Buffer.from(data.slice(1,17)).toString("hex");

          const uuid = Buffer.from(data.slice(83,99)).toString("hex");
          const lengths = [8,4,4,4,12];
          let parts = [];
          let range = 0;
          for (var i = 0; i < lengths.length; i++) {
              parts.push(uuid.slice(range,range+lengths[i]).toUpperCase());
              range += lengths[i];
          };
          this.uuid = parts.join('-');

          this.done = true
        },
      }
    }
</script>
