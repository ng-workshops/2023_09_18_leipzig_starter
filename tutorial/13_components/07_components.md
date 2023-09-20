# 7 Component interaction - Message service

> npx ng generate service home/message

## src/app/home/message.service.ts

```ts
import { Injectable } from '@angular/core';
import { Subject } from 'rxjs';

@Injectable({
  providedIn: 'root',
})
export class MessageService {
  // Observable string source
  private listener = new Subject<string>();

  // Observable string stream
  listener$ = this.listener.asObservable();

  // Service message commands
  sendMessage(message: string) {
    this.listener.next(message);
  }
}
```

## src/app/home/info-box/info-box.component.ts

```ts
import {
  Component,
  OnInit,
  Input,
  OnChanges,
  SimpleChange,
  Output,
  EventEmitter,
  OnDestroy,
} from '@angular/core';
import { MessageService } from '../message.service';
import { Subscription } from 'rxjs';

@Component({
  selector: 'app-info-box',
  standalone: true,
  imports: [MatCardModule, CommonModule],
  templateUrl: './info-box.component.html',
  styleUrls: ['./info-box.component.scss'],
})
export class InfoBoxComponent implements OnInit, OnChanges, OnDestroy {
  private _name!: string;
  private subscription!: Subscription;
  private messageService = inject(MessageService);

  @Input()
  message: string | undefined;

  @Input()
  set name(value: string) {
    this._name = value.toLowerCase();
  }

  get name(): string {
    return this._name;
  }

  @Output()
  replyToParent = new EventEmitter<string>();

  ngOnInit() {
    this.subscription = this.messageService.listener$.subscribe(
      (msg) => (this.message = msg)
    );
  }

  ngOnChanges(changes: Record<string, SimpleChange>) {
    if (changes['message']) {
      console.log('changes.message', changes['message'].currentValue);
    }

    if (changes['name']) {
      console.log('changes.name', changes['name'].currentValue);
    }

    if (changes['message'] && changes['name']) {
      console.log('Message AND Name changed');
    }
  }

  ngOnDestroy() {
    if (this.subscription) {
      this.subscription.unsubscribe();
    }
  }

  reply(message?: string) {
    this.replyToParent.emit(message || 'Message from Child');
  }
}
```

## src/app/home/home.component.html

```html
<p>
  <button (click)="changeChild()">Change Child data</button>
  <button (click)="child.name = 'Changed BY PARENT'">
    Change Child via Template Var
  </button>
  <button (click)="processReplyFromCode()">Change Child via ViewChild</button>
  <button (click)="sendMessage()">Send message via service</button>
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
import { MessageService } from './message.service';

@Component({
  selector: 'app-home',
  standalone: true,
  imports: [CommonModule, FormsModule, InfoBoxComponent],
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.scss'],
})
export class HomeComponent {
  private messageService = inject(MessageService);

  message = 'INIT';
  name = 'START_';
  reply = '';

  @ViewChild('child')
  private child!: InfoBoxComponent;

  changeChild() {
    this.message = new Date().toISOString();
    this.name += 'X';
  }

  processReply(event: string) {
    this.reply = event;
  }

  processReplyFromCode() {
    this.child.reply('Send from parent via CODE');
  }

  sendMessage() {
    this.messageService.sendMessage('Send from parent via service');
  }
}
```
