# 8 Component - 2 way binding

> npx ng generate component home/info-item --standalone=true

## src/app/home/info-item/info-item.component.html

```html
<p>
  <input type="text" [(ngModel)]="message" />
</p>
```

## src/app/home/info-item/info-item.component.ts

```ts
import { Component, EventEmitter, Input, Output } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';

@Component({
  selector: 'app-info-item',
  standalone: true,
  imports: [CommonModule, FormsModule],
  templateUrl: './info-item.component.html',
  styleUrls: ['./info-item.component.scss'],
})
export class InfoItemComponent {
  private _messageValue!: string;

  @Output()
  messageChange = new EventEmitter<string>();

  @Input()
  get message() {
    return this._messageValue;
  }

  set message(val) {
    this._messageValue = val;
    this.messageChange.emit(this._messageValue);
  }
}
```

## src/app/home/home.component.html

```html
<p>
  <app-info-item [(message)]="name"></app-info-item>
</p>
```

## src/app/home/home.component.ts

```ts
@Component({
  selector: 'app-home',
  standalone: true,
  imports: [CommonModule, FormsModule, InfoBoxComponent, InfoItemComponent],
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.scss'],
})
```
