<script lang="ts">
  import { onDestroy } from 'svelte'
  import { tweaksStorage } from './Tweaks.svelte'
  import type { TweaksState } from './Types.svelte'

  let tweaks:TweaksState
  $: tweaks = $tweaksStorage as TweaksState

  const scriptLines: string[] = [
    '# archinstall',
    '',
    'Loading installer modules...',
    ' -> disks        [OK]',
    ' -> network      [OK]',
    ' -> locales      [OK]',
    '',
    'Probing disks...',
    ' /dev/nvme0n1    476.9G  (GPT)',
    ' /dev/sda        931.5G  (GPT)',
    '',
    'Creating partition layout on /dev/nvme0n1...',
    ' - wiping existing partition table         [OK]',
    ' - creating EFI system partition (512M)    [OK]',
    ' - creating root partition (rest of disk)  [OK]',
    '',
    'Formatting file systems...',
    ' mkfs.vfat -F32 /dev/nvme0n1p1             [OK]',
    ' mkfs.ext4      /dev/nvme0n1p2             [OK]',
    '',
    'Mounting target root...',
    ' mount /dev/nvme0n1p2 /mnt                 [OK]',
    ' mkdir /mnt/boot                            [OK]',
    ' mount /dev/nvme0n1p1 /mnt/boot            [OK]',
    '',
    'Syncing package databases...',
    ' pacman -Sy                                 [OK]',
    '',
    'Installing base system (base linux linux-firmware)...',
    ' downloading packages                       [OK]',
    ' checking package integrity                 [OK]',
    ' installing packages                        [OK]',
    '',
    'Generating fstab...',
    ' genfstab -U /mnt >> /mnt/etc/fstab         [OK]',
    '',
    'Chrooting into new system...',
    ' arch-chroot /mnt                           [OK]',
    '',
    'Setting timezone, locale, hostname...',
    ' ln -sf /usr/share/zoneinfo/UTC /etc/localtime   [OK]',
    ' hwclock --systohc                               [OK]',
    ' locale-gen                                      [OK]',
    '',
    'Installing bootloader (systemd-boot)...',
    ' bootctl --path=/boot install                   [OK]',
    '',
    'Finalizing...',
    ' Unmounting partitions...',
    '  umount -R /mnt                                [OK]',
    '',
    'Arch Linux installation complete.',
    '',
    'You may now close this window and tell everyone you use Arch btw.'
  ]

  let visible = false
  let lines: string[] = []
  let idx = 0
  let timer: number | undefined

  const stop = () => {
    if (timer) {
      clearInterval(timer)
      timer = undefined
    }
  }

  const start = () => {
    stop()
    lines = []
    idx = 0
    visible = true
    const totalDuration = Math.min(45000, scriptLines.length * 500)
    timer = setInterval(() => {
      if (idx >= scriptLines.length) {
        stop()
        return
      }
      lines = [...lines, scriptLines[idx]]
      idx++
    }, 350) as unknown as number
    // Auto-disable the emulator tweak after the script finishes
    setTimeout(() => {
      tweaksStorage.update((state) => ({
        ...state,
        archInstallEmulator: false
      }))
      visible = false
      stop()
    }, totalDuration)
  }

  $: if (tweaks?.archInstallEmulator && !visible) {
    start()
  }
  $: if (!tweaks?.archInstallEmulator && visible) {
    visible = false
    stop()
  }

  onDestroy(() => {
    stop()
  })
</script>

{#if visible}
  <div class="arch-install-overlay" on:click|stopPropagation>
    <div class="arch-install-terminal">
      <div class="arch-install-header">
        <span class="dot red"></span>
        <span class="dot yellow"></span>
        <span class="dot green"></span>
        <span class="title">archinstall — pseudo TTY</span>
      </div>
      <div class="arch-install-body">
        {#each lines as line}
          <pre class="line">{line}</pre>
        {/each}
        <pre class="cursor">_</pre>
      </div>
      <div class="arch-install-footer">
        <span>Tip: this is just a visual joke – no disks were harmed.</span>
      </div>
    </div>
  </div>
{/if}

