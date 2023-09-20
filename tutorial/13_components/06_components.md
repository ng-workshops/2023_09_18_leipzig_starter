# 6 Component interaction - @ViewChild()

## src/app/home/home.component.html

```html
<p>
  <button (click)="changeChild()">Change Child data</button>
  <button (click)="child.name = 'Changed BY PARENT'">
    Change Child via Template Variable
  </button>
  <button (click)="processReplyFromCode()">Change Child via ViewChild</button>
</p>

<app-info-box
  #child
  [message]="message"
  [name]="name"
  (replyToParent)="processReply($event)"
></app-info-box>

<pre>Message from Child = {{ reply | json }}</pre>
```

## src/app/home/home.component.ts

```ts
import { Component, ViewChild } from '@angular/core';
import { InfoBoxComponent } from './info-box/info-box.component';

@Component({
  selector: 'app-home',
  standalone: true,
  imports: [CommonModule, FormsModule, InfoBoxComponent],
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.scss'],
})
export class HomeComponent {
  message = 'INIT';
  name = 'START_';
  reply = '';

  @ViewChild('child')
  private child!: InfoBoxComponent;

  changeChild() {
    this.message = new Date().toISOString();
    this.name += 'X';
  }

  processReply(event) {
    this.reply = event;
  }

  processReplyFromCode() {
    this.child.reply('Send from parent via CODE');
    this.name += 'Send from parent via CODE';
  }
}
```
